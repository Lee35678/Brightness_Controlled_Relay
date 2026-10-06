# Brightness_Controlled_Relay

ESP32에서 밝기 센서 값이 임계값보다 낮으면(어두우면) 릴레이를 켜는 PlatformIO 예제

## 개요

아날로그 밝기 센서 값을 0.5초마다 읽어, 어두우면 릴레이를 켜고 밝으면 끕니다. 릴레이에 조명 같은 전기기구를 연결해 주변이 어두워지면 자동으로 켜지게 하는 구조입니다. 작성 시기는 2024년 9월입니다(커밋 기록 기준).

## 하드웨어

- 보드: DOIT ESP32 DevKit V1 (`board = esp32doit-devkit-v1`)
- 입력: 아날로그 출력형 밝기 센서 1개
- 출력: 릴레이 모듈 1개

| 신호 | GPIO | 설정 |
|------|------|------|
| 릴레이 (`RELAY`) | 23 | `OUTPUT` |
| 밝기 센서 (`LDR_PIN`) | 15 | `analogRead` |

## 동작 방식

1. `setup()`: 시리얼을 115200 bps로 열고 500 ms 대기 후 `Starting`을 출력한 뒤, 릴레이 핀을 출력으로 설정합니다.
2. `loop()`: 500 ms마다 다음을 반복합니다.
   - `analogRead(LDR_PIN)`으로 밝기 값을 읽고 시리얼에 출력합니다.
   - 임계값(`threshold = 500`)과 비교해 릴레이를 제어합니다.

| 조건 | 상태 | 릴레이 |
|------|------|--------|
| `light_val < 500` | 어두움 | HIGH (켜짐) |
| `light_val >= 500` | 밝음 | LOW (꺼짐) |

## 개발 환경

| 항목 | 값 |
|------|----|
| 도구 | PlatformIO |
| 플랫폼 | `espressif32` |
| 프레임워크 | `arduino` |
| 외부 라이브러리 | 없음 |
| 모니터 속도 | `monitor_speed = 115200` |

## 빌드 및 업로드

```bash
pio run -t upload
pio device monitor
```

## 폴더 구조

```
Brightness_Controlled_Relay/
├── platformio.ini
└── src/
    └── main.cpp
```

## 참고

- 임계값 하나로만 판단하고 히스테리시스나 필터가 없어서, 센서 값이 500 근처에서 흔들리면 릴레이가 0.5초 간격으로 계속 켜졌다 꺼질 수 있습니다. 이 문제를 이동평균 필터로 줄인 버전이 Low_Pass_Filter 프로젝트입니다.
- 임계값 500은 코드에 고정되어 있으므로 센서와 환경에 맞게 `threshold` 값을 바꿔야 합니다.
