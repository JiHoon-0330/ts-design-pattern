# 상태 패턴
오디오 컨트롤러를 상태 패턴으로 만들어보기

## Class

```ts
abstract class PlayerState {
  protected player: AudioPlayer;

  constructor(player: AudioPlayer) {
    this.player = player;
  }

  abstract play(): void;
  abstract pause(): void;
  abstract stop(): void;
}

class PlayingState extends PlayerState {
  constructor(player: AudioPlayer) {
    super(player);
  }

  play(): void {
    console.log("이미 재생 중입니다.");
  }

  pause(): void {
    console.log("음악을 일시 정지합니다.");
    this.player.changeState(new PausedState(this.player));
  }

  stop(): void {
    console.log("음악을 정지합니다.");
    this.player.changeState(new StoppedState(this.player));
  }
}

class PausedState extends PlayerState {
  constructor(player: AudioPlayer) {
    super(player);
  }

  play(): void {
    console.log("음악을 이어서 재생합니다.");
    this.player.changeState(new PlayingState(this.player));
  }

  pause(): void {
    console.log("이미 일시 정지 중입니다.");
  }

  stop(): void {
    console.log("음악을 정지합니다.");
    this.player.changeState(new StoppedState(this.player));
  }
}

class StoppedState extends PlayerState {
  constructor(player: AudioPlayer) {
    super(player);
  }

  play(): void {
    console.log("새로운 음악을 재생합니다.");
    this.player.changeState(new PlayingState(this.player));
  }

  pause(): void {
    console.log("정지 상태에서는 일시 정지할 수 없습니다. 먼저 재생하세요.");
  }

  stop(): void {
    console.log("이미 정지 중입니다.");
  }
}

class AudioPlayer {
  private state: PlayerState;

  constructor() {
    this.state = new StoppedState(this);
  }

  public changeState(state: PlayerState): void {
    this.state = state;
    console.log(`현재 상태: ${state.constructor.name}`);
  }

  public pressPlay(): void {
    this.state.play();
  }

  public pressPause(): void {
    this.state.pause();
  }

  public pressStop(): void {
    this.state.stop();
  }
}

const player = new AudioPlayer();

console.log("--- 첫 번째 시나리오 ---");
player.pressPlay();
player.pressPause();
player.pressPlay();
player.pressStop();

console.log("\n--- 두 번째 시나리오 ---");
player.pressStop();
player.pressPause();
player.pressPlay();
player.pressPlay();

--- 첫 번째 시나리오 ---
새로운 음악을 재생합니다.
현재 상태: PlayingState
음악을 일시 정지합니다.
현재 상태: PausedState
음악을 이어서 재생합니다.
현재 상태: PlayingState
음악을 정지합니다.
현재 상태: StoppedState

--- 두 번째 시나리오 ---
이미 정지 중입니다.
정지 상태에서는 일시 정지할 수 없습니다. 먼저 재생하세요.
새로운 음악을 재생합니다.
현재 상태: PlayingState
이미 재생 중입니다.
```

## Function

```ts
interface IPlayerState {
  name: string;
  play: () => void;
  pause: () => void;
  stop: () => void;
}

interface IAudioPlayerContextCallbacks {
  changeState: (newStateFn: IPlayerState) => void;
}

function createPlayingState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "PlayingState",
    play: () => {
      console.log("이미 재생 중입니다.");
    },
    pause: () => {
      console.log("음악을 일시 정지합니다.");
      callbacks.changeState(createPausedState(callbacks));
    },
    stop: () => {
      console.log("음악을 정지합니다.");
      callbacks.changeState(createStoppedState(callbacks));
    },
  };
}

function createPausedState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "PausedState",
    play: () => {
      console.log("음악을 이어서 재생합니다.");
      callbacks.changeState(createPlayingState(callbacks));
    },
    pause: () => {
      console.log("이미 일시 정지 중입니다.");
    },
    stop: () => {
      console.log("음악을 정지합니다.");
      callbacks.changeState(createStoppedState(callbacks));
    },
  };
}

function createStoppedState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "StoppedState",
    play: () => {
      console.log("새로운 음악을 재생합니다.");
      callbacks.changeState(createPlayingState(callbacks));
    },
    pause: () => {
      console.log("정지 상태에서는 일시 정지할 수 없습니다. 먼저 재생하세요.");
    },
    stop: () => {
      console.log("이미 정지 중입니다.");
    },
  };
}

function createAudioPlayer() {
  let currentState: IPlayerState;

  const changeState = (newStateFn: IPlayerState) => {
    currentState = newStateFn;
    console.log(`현재 상태: ${newStateFn.name}`);
  };

  currentState = createStoppedState({ changeState });

  return {
    pressPlay: () => {
      currentState.play();
    },
    pressPause: () => {
      currentState.pause();
    },
    pressStop: () => {
      currentState.stop();
    },
  };
}

const player = createAudioPlayer();

console.log("--- 첫 번째 시나리오 ---");
player.pressPlay();
player.pressPause();
player.pressPlay();
player.pressStop();

console.log("\n--- 두 번째 시나리오 ---");
player.pressStop();
player.pressPause();
player.pressPlay();
player.pressPlay();

interface IPlayerState {
  name: string;
  play: () => void;
  pause: () => void;
  stop: () => void;
}

interface IAudioPlayerContextCallbacks {
  changeState: (newStateFn: IPlayerState) => void;
}

function createPlayingState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "PlayingState",
    play: () => {
      console.log("이미 재생 중입니다.");
    },
    pause: () => {
      console.log("음악을 일시 정지합니다.");
      callbacks.changeState(createPausedState(callbacks));
    },
    stop: () => {
      console.log("음악을 정지합니다.");
      callbacks.changeState(createStoppedState(callbacks));
    },
  };
}

function createPausedState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "PausedState",
    play: () => {
      console.log("음악을 이어서 재생합니다.");
      callbacks.changeState(createPlayingState(callbacks));
    },
    pause: () => {
      console.log("이미 일시 정지 중입니다.");
    },
    stop: () => {
      console.log("음악을 정지합니다.");
      callbacks.changeState(createStoppedState(callbacks));
    },
  };
}

function createStoppedState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "StoppedState",
    play: () => {
      console.log("새로운 음악을 재생합니다.");
      callbacks.changeState(createPlayingState(callbacks));
    },
    pause: () => {
      console.log("정지 상태에서는 일시 정지할 수 없습니다. 먼저 재생하세요.");
    },
    stop: () => {
      console.log("이미 정지 중입니다.");
    },
  };
}

function createAudioPlayer() {
  let currentState: IPlayerState;

  const changeState = (newStateFn: IPlayerState) => {
    currentState = newStateFn;
    console.log(`현재 상태: ${newStateFn.name}`);
  };

  currentState = createStoppedState({ changeState });

  return {
    pressPlay: () => {
      currentState.play();
    },
    pressPause: () => {
      currentState.pause();
    },
    pressStop: () => {
      currentState.stop();
    },
  };
}

const player = createAudioPlayer();

console.log("--- 첫 번째 시나리오 ---");
player.pressPlay();
player.pressPause();
player.pressPlay();
player.pressStop();

console.log("\n--- 두 번째 시나리오 ---");
player.pressStop();
player.pressPause();
player.pressPlay();
player.pressPlay();

--- 첫 번째 시나리오 ---
새로운 음악을 재생합니다.
현재 상태: PlayingState
음악을 일시 정지합니다.
현재 상태: PausedState
음악을 이어서 재생합니다.
현재 상태: PlayingState
음악을 정지합니다.
현재 상태: StoppedState

--- 두 번째 시나리오 ---
이미 정지 중입니다.
정지 상태에서는 일시 정지할 수 없습니다. 먼저 재생하세요.
새로운 음악을 재생합니다.
현재 상태: PlayingState
이미 재생 중입니다.
```

## JSX

```ts
import { useState } from "react";

interface IPlayerState {
  name: string;
  play: () => void;
  pause: () => void;
  stop: () => void;
}

interface IAudioPlayerContextCallbacks {
  changeState: (newStateFn: IPlayerState) => void;
}

function createPlayingState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "PlayingState",
    play: () => {
      console.log("이미 재생 중입니다.");
    },
    pause: () => {
      console.log("음악을 일시 정지합니다.");
      callbacks.changeState(createPausedState(callbacks));
    },
    stop: () => {
      console.log("음악을 정지합니다.");
      callbacks.changeState(createStoppedState(callbacks));
    },
  };
}

function createPausedState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "PausedState",
    play: () => {
      console.log("음악을 이어서 재생합니다.");
      callbacks.changeState(createPlayingState(callbacks));
    },
    pause: () => {
      console.log("이미 일시 정지 중입니다.");
    },
    stop: () => {
      console.log("음악을 정지합니다.");
      callbacks.changeState(createStoppedState(callbacks));
    },
  };
}

function createStoppedState(
  callbacks: IAudioPlayerContextCallbacks,
): IPlayerState {
  return {
    name: "StoppedState",
    play: () => {
      console.log("새로운 음악을 재생합니다.");
      callbacks.changeState(createPlayingState(callbacks));
    },
    pause: () => {
      console.log("정지 상태에서는 일시 정지할 수 없습니다. 먼저 재생하세요.");
    },
    stop: () => {
      console.log("이미 정지 중입니다.");
    },
  };
}

function AudioPlayer() {
  const [currentState, setCurrentState] = useState<IPlayerState>(() =>
    createStoppedState({
      changeState: (newStateFn: IPlayerState) => {
        setCurrentState(newStateFn);
      },
    }),
  );

  return (
    <>
      <button
        type="button"
        onClick={() => {
          currentState.play();
        }}
      >
        재생
      </button>
      <button
        type="button"
        onClick={() => {
          currentState.pause();
        }}
      >
        일시정지
      </button>
      <button
        type="button"
        onClick={() => {
          currentState.stop();
        }}
      >
        정지
      </button>
    </>
  );
}
```