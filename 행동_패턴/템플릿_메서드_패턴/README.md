# 전략 패턴
버튼 클릭 핸들러를 템플릿 메서드 패턴으로 만들어보기

## Class

```ts
abstract class AbstractButtonTemplateMethod {
  public handleClick(): void {
    this.beforeClick();
    this.onClick();
    this.afterClick();
  }

  abstract beforeClick(): void;
  abstract onClick(): void;
  abstract afterClick(): void;
}

class ButtonTemplateMethod extends AbstractButtonTemplateMethod {
  beforeClick(): void {
    console.log("beforeClick");
  }

  onClick(): void {
    console.log("onClick");
  }

  afterClick(): void {
    console.log("afterClick");
  }
}

const buttonTemplateMethod = new ButtonTemplateMethod();
buttonTemplateMethod.handleClick();

---출력결과---
beforeClick
onClick
afterClick
```

## Function

```ts
interface IButtonTemplateMethod {
  beforeClick(): void;
  onClick(): void;
  afterClick(): void;
}

function createButtonOnClickHandler(options: IButtonTemplateMethod) {
  return () => {
    options.beforeClick();
    options.onClick();
    options.afterClick();
  };
}

const buttonOnClickHandler = createButtonOnClickHandler({
  beforeClick: () => {
    console.log("beforeClick");
  },
  onClick: () => {
    console.log("onClick");
  },
  afterClick: () => {
    console.log("afterClick");
  },
});

buttonOnClickHandler();

---출력결과---
beforeClick
onClick
afterClick
```

## JSX

```ts
interface Props {
  beforeClick(): void;
  onClick(): void;
  afterClick(): void;
}

function Button({ beforeClick, onClick, afterClick }: Props) {
  return (
    <button
      type="button"
      onClick={() => {
        beforeClick();
        onClick();
        afterClick();
      }}
    >
      버튼
    </button>
  );
}
```