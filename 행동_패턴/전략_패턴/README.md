# 전략 패턴
Android와 iOS 결제를 전략 패턴으로 만들어보기

## Class

```ts
interface IPaymentStrategy {
  processPayment(amount: number): void;
}

class AndroidPaymentStrategy implements IPaymentStrategy {
  processPayment(amount: number): void {
    console.log(`Android 결제를 ${amount}원 처리합니다.`);
  }
}

class IOSPaymentStrategy implements IPaymentStrategy {
  processPayment(amount: number): void {
    console.log(`iOS 결제를 ${amount}원 처리합니다.`);
  }
}

class PaymentContext {
  private paymentStrategy: IPaymentStrategy;

  constructor(strategy: IPaymentStrategy) {
    this.paymentStrategy = strategy;
  }

  public setPaymentStrategy(strategy: IPaymentStrategy): void {
    this.paymentStrategy = strategy;
  }

  public executePayment(amount: number): void {
    this.paymentStrategy.processPayment(amount);
  }
}

const androidPaymentContext = new PaymentContext(new AndroidPaymentStrategy());
console.log("--- Android 결제 ---");
androidPaymentContext.executePayment(10000);

console.log("\n--- iOS 결제로 변경 후 결제 ---");
androidPaymentContext.setPaymentStrategy(new IOSPaymentStrategy());
androidPaymentContext.executePayment(25000);

---실행결과---

--- Android 결제 ---
Android 결제를 10000원 처리합니다.

--- iOS 결제로 변경 후 결제 ---
iOS 결제를 25000원 처리합니다.
```

## Function

```ts
interface IPaymentStrategy {
  processPayment(amount: number): void;
}

const androidPaymentStrategy = (): IPaymentStrategy => ({
  processPayment(amount: number): void {
    console.log(`Android 결제를 ${amount}원 처리합니다.`);
  },
});

const iosPaymentStrategy = (): IPaymentStrategy => ({
  processPayment(amount: number): void {
    console.log(`iOS 결제를 ${amount}원 처리합니다.`);
  },
});

const paymentContext = (strategy: IPaymentStrategy) => {
  let paymentStrategy = strategy;

  return {
    setPaymentStrategy: (strategy: IPaymentStrategy) => {
      paymentStrategy = strategy;
    },
    executePayment: (amount: number) => {
      paymentStrategy.processPayment(amount);
    },
  };
};

const androidPaymentContext = paymentContext(androidPaymentStrategy());
console.log("--- Android 결제 ---");
androidPaymentContext.executePayment(10000);

console.log("\n--- iOS 결제로 변경 후 결제 ---");
androidPaymentContext.setPaymentStrategy(iosPaymentStrategy());
androidPaymentContext.executePayment(25000);

---실행결과---

--- Android 결제 ---
Android 결제를 10000원 처리합니다.

--- iOS 결제로 변경 후 결제 ---
iOS 결제를 25000원 처리합니다.
```

## JSX

```ts
interface IPaymentStrategy {
  processPayment(amount: number): void;
}

type PaymentType = "android" | "ios";

const paymentStrategyRecord: Record<PaymentType, IPaymentStrategy> = {
  android: {
    processPayment(amount: number): void {
      console.log(`Android 결제를 ${amount}원 처리합니다.`);
    },
  },
  ios: {
    processPayment(amount: number): void {
      console.log(`iOS 결제를 ${amount}원 처리합니다.`);
    },
  },
};

interface Props {
  paymentType: PaymentType;
}

function PaymentStrategy({ paymentType }: Props) {
  const paymentStrategy = paymentStrategyRecord[paymentType];

  return (
    <button
      type="button"
      onClick={() => {
        paymentStrategy.processPayment(10000);
      }}
    >
      {paymentType}에서 결제하기
    </button>
  );
}
```