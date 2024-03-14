# 스케줄링: 개요

이제 프로세스를 실행하기 위해 필요한 문맥 교환(Context Switch)과 같은 저수준 기술에 대해 명확히 이해하고 있어야 합니다. 만약 이러한 기술들의 동작 방식에 대해 잘 모르겠다면, 앞서 배운 내용을 다시 복습하는 것이 좋습니다. 또한, 운영체제 스케줄러의 고수준 정책에 대한 이해도 필요합니다.

지금부터는 다양한 스케줄링 정책(Scheduling Policy)을 소개하고, 이에 대한 이해를 높이는 데 집중하겠습니다. 이러한 정책은 스케줄링 기법(Scheduling Discipline)이라고도 불리며, 많은 優秀하고 부지런한 연구자들이 오랜 시간 동안 개발해 온 것들입니다.

사실, 스케줄링의 기원은 컴퓨터 시스템이 개발되기 이전으로 거슬러 올라갑니다. 초기의 스케줄링 방법들은 생산 관리(Production Management) 분야에서 개발되었고, 이후 컴퓨터 시스템에 적용되었습니다. 이는 그리 놀라운 일이 아닙니다. 생산 공정과 인간의 다양한 활동에서도 스케줄링이 필요하고, 효율성 향상과 같은 공통된 목표를 가지고 있기 때문입니다.

우리가 해결해야 할 핵심 질문은 다음과 같습니다:

1. 스케줄링 정책은 어떻게 개발할 수 있을까요?
2. 스케줄링 정책을 설계하기 위한 기본적인 프레임워크는 무엇일까요?
3. 정책 개발에 필요한 핵심 가정은 무엇일까요?
4. 어떤 평가 기준이 중요할까요?
5. 컴퓨터 시스템 초창기에 사용된 기본 접근 방식은 무엇일까요?

이러한 질문에 대한 답을 찾아가면서, 스케줄링 정책의 개발 과정과 그 기반이 되는 개념들을 이해할 수 있을 것입니다. 이를 통해 운영체제의 스케줄링 메커니즘에 대한 포괄적인 지식을 갖추게 될 것입니다.

## 워크로드에 대한 가정

스케줄링 정책을 개발하기에 앞서, 우리는 프로세스에 대해 몇 가지 가정을 해야 합니다. 이러한 가정들은 시스템에서 실행되는 프로세스들의 집합인 워크로드(Workload)를 정의하는 데 도움을 줍니다. 워크로드에 대해 잘 이해할수록 그에 맞는 최적의 스케줄링 정책을 설계할 수 있습니다.

우리는 시스템에서 실행 중인 프로세스 또는 작업에 대해 다음과 같은 가정을 합니다:

1. 모든 작업의 실행 시간은 동일합니다.

   - 예를 들어, 모든 작업이 정확히 10초 동안 실행된다고 가정합니다.

2. 모든 작업은 동시에 시스템에 도착합니다.

   - 즉, 모든 작업이 시작 시점에 한꺼번에 도착한다는 것입니다.

3. 각 작업은 한 번 시작되면 중간에 중단되지 않고 완료될 때까지 실행됩니다.

   - 작업이 실행되는 동안 다른 작업으로 전환되지 않습니다.

4. 모든 작업은 오직 CPU 자원만 사용합니다.

   - 작업 실행 중에는 입출력(I/O) 작업이 발생하지 않는다고 가정합니다.

5. 각 작업의 실행 시간은 스케줄러가 미리 알고 있습니다.
   - 스케줄러는 작업의 실행 시간을 사전에 파악하고 있다고 가정합니다.

하지만 이러한 가정들은 현실 세계와는 거리가 멉니다. 특히 작업의 실행 시간을 미리 알고 있다는 가정은 매우 비현실적입니다. 이는 마치 스케줄러가 모든 것을 알고 있다고 가정하는 것과 같습니다.

이러한 가정들은 스케줄링 정책을 단순화하여 이해하기 쉽게 만들어 주지만, 실제 시스템에 적용할 때는 현실적인 제약 사항들을 고려해야 합니다. 그럼에도 불구하고, 이 가정들은 스케줄링 정책의 기본 개념을 이해하는 데 도움을 줍니다.

## 스케줄링 평가 항목

## 선입선출

## 최단 작업 우선

## 최소 잔여시간 우선

## 새로운 평가 기준 : 응답 시간

## 라운드 로빈

## 입출력 연산의 고려

## 만병통치약은 없다 (No More Oracle)

## 요약