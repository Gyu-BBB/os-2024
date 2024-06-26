# 페이징: 더 빠른 변환 (TLB)

페이징은 가상 메모리 관리 기법 중 하나로, 프로세스의 주소 공간을 일정한 크기의 페이지 단위로 나누어 관리합니다. 이 기법은 메모리 활용도를 높이고 프로세스 간 메모리 보호를 제공하는 등 많은 장점이 있지만, 성능 저하를 야기할 수 있는 단점도 있습니다.

페이징에서는 각 페이지의 실제 메모리 상의 위치를 나타내는 매핑 정보를 페이지 테이블이라는 자료구조에 저장합니다. 이 페이지 테이블은 메모리에 상주하므로, 프로세스가 가상 주소를 사용하여 메모리에 접근할 때마다 페이지 테이블을 참조하여 실제 물리 주소를 얻어야 합니다. 이를 주소 변환(Address Translation)이라고 합니다.

문제는 이 주소 변환 과정에서 추가적인 메모리 접근이 발생한다는 점입니다. 프로세스가 메모리에 접근할 때마다 페이지 테이블을 읽어야 하므로, 매 메모리 접근마다 두 번의 메모리 연산(페이지 테이블 읽기 + 실제 데이터 읽기/쓰기)이 필요하게 됩니다. 이는 심각한 성능 저하를 초래할 수 있습니다.

이 문제를 해결하기 위해 우리는 다음과 같은 질문을 해볼 수 있습니다:

**핵심 질문:**

1. 주소 변환 속도를 어떻게 향상시킬 수 있을까?
2. 페이징에서 발생하는 추가 메모리 참조를 어떻게 피할 수 있을까?
3. 이를 위해 어떤 하드웨어 지원이 필요할까?
4. 운영체제는 어떤 식으로 관여해야 할까?

이 문제를 해결하기 위해, 우리는 하드웨어의 도움을 받을 수 있습니다. 바로 **Translation Lookaside Buffer (TLB)** 라는 특별한 하드웨어 캐시를 사용하는 것입니다.

TLB는 CPU의 Memory Management Unit (MMU) 내에 위치한 작고 빠른 하드웨어 캐시입니다. TLB의 목적은 최근에 사용된 가상 주소와 물리 주소 간의 매핑 정보를 저장하여, 주소 변환 속도를 향상시키는 것입니다.

프로세스가 가상 주소를 사용하여 메모리에 접근하면, 하드웨어는 먼저 TLB에서 해당 가상 주소의 변환 정보를 찾아봅니다. 만약 TLB에 정보가 있다면 (TLB Hit), 곧바로 물리 주소를 얻어 메모리에 접근할 수 있습니다. 이 경우, 페이지 테이블을 읽는 추가 작업 없이 주소 변환을 완료할 수 있으므로 매우 빠릅니다.

반면에, 원하는 정보가 TLB에 없는 경우 (TLB Miss)에는 기존대로 페이지 테이블을 참조하여 주소 변환을 진행해야 합니다. 이 과정에서 얻은 변환 정보는 향후 재사용을 위해 TLB에 저장됩니다.

적절한 크기의 TLB를 사용하면, 대부분의 메모리 접근이 TLB Hit로 처리되므로 전체적인 주소 변환 속도가 크게 향상됩니다. 이렇게 TLB를 사용함으로써 페이징의 성능 문제를 완화하고, 가상 메모리를 실질적으로 사용 가능하게 만들 수 있습니다.

**주요 용어 정리**

- **페이징 (Paging)**: 가상 메모리를 일정한 크기의 페이지 단위로 나누어 관리하는 기법.
- **페이지 (Page)**: 가상 메모리를 일정한 크기로 나눈 블록. 주소 변환의 기본 단위가 됨.
- **페이지 테이블 (Page Table)**: 각 페이지의 가상 주소와 실제 물리 주소 간의 매핑 정보를 저장하는 자료구조.
- **주소 변환 (Address Translation)**: 가상 주소를 물리 주소로 변환하는 과정.
- **Translation Lookaside Buffer (TLB)**: 주소 변환을 가속하기 위한 하드웨어 캐시. 최근 사용된 가상-물리 주소 매핑 정보를 저장함.
- **TLB Hit**: 원하는 주소 변환 정보가 TLB에 존재하는 경우.
- **TLB Miss**: 원하는 주소 변환 정보가 TLB에 없는 경우. 페이지 테이블을 참조하여 변환을 진행해야 함.

운영체제와 하드웨어의 긴밀한 협력을 통해, 우리는 페이징의 단점을 극복하고 가상 메모리의 장점을 충분히 활용할 수 있습니다. TLB와 같은 하드웨어 지원은 운영체제의 성능을 향상시키는 데 핵심적인 역할을 합니다.

## TLB의 기본 알고리즘

1. TLB 검색 과정
   가상 주소가 제공되면, 해당 주소의 가상 페이지 번호(VPN)를 추출하고, TLB에서 이 VPN에 해당하는 엔트리를 병렬적으로 검색한다. 일치하는 엔트리가 있는 경우, 그 엔트리에 포함된 물리 페이지 번호(PPN)로 물리 주소를 계산한다.

2. TLB 히트
   유효한 엔트리가 존재하면, 즉시 물리 주소를 생성하고 메모리에 액세스한다. 이 과정을 TLB 히트라고 한다.

3. TLB 미스
   일치하는 엔트리가 없을 경우, TLB 미스가 발생하며, 페이지 테이블을 참조하여 가상 주소를 물리 주소로 변환한다. 변환된 주소는 TLB에 추가되어 다음 접근 시 빠르게 찾을 수 있도록 한다.

## 예제: 배열 접근

```c
int main() {
    int array[100];
    array[50] = 10;  // 배열의 특정 위치에 데이터 쓰기
    return 0;
```

이 코드에서 array[50]에 값을 할당하려면 다음과 같은 과정이 필요합니다.

1. 가상 주소 0x1400 (50 \* 4) 생성
2. TLB 검색: 0x1400에 대한 엔트리가 존재하는지 확인
   - TLB 히트: 엔트리가 존재하면 엔트리에 포함된 물리 페이지 번호를 사용하여 물리 주소를 생성하고 메모리에 액세스
   - TLB 미스: 엔트리가 존재하지 않으면 페이지 테이블 워크를 통해 변환 정보를 검색
3. 페이지 테이블 워크: 페이지 테이블에서 가상 페이지 번호 0x3C에 대한 엔트리를 찾아 물리 페이지 번호를 얻음
4. TLB에 엔트리 추가: 0x1400 (VPN)과 0x0A00 (PPN)으로 구성된 엔트리를 TLB에 추가
5. 물리 주소 생성: 물리 페이지 번호 0x0A00과 오프셋 0x00을 사용하여 물리 주소 0xA000 생성
6. 메모리 액세스: 0xA000 주소에 값 10 저장

## TLB 미스는 누가 처리할까

TLB 미스는 Translation Lookaside Buffer(번역 조회 버퍼)에서 원하는 주소 변환 정보를 찾지 못할 때 발생하는 상황입니다. TLB 미스를 처리하는 방법은 두 가지가 있습니다: 하드웨어 관리와 소프트웨어(운영 체제) 관리입니다.

하드웨어 관리 방식은 주로 복잡한 명령어 집합인 CISC(complex instruction set computers) 구조에서 사용됩니다. 하드웨어가 TLB 미스를 처리하려면 페이지 테이블에 대한 정보를 갖고 있어야 합니다. TLB 미스가 발생하면 하드웨어가 페이지 테이블에서 필요한 정보를 추출하고 TLB를 갱신한 후 명령어를 재실행합니다.

반면에 소프트웨어 관리 방식은 최근에 등장한 RISC(reduced instruction set computing) 구조에서 주로 사용됩니다. TLB 미스가 발생하면 하드웨어가 예외 시그널을 발생시키고, 이를 받은 운영 체제가 커널 모드로 전환하여 트랩 핸들러를 실행합니다. 트랩 핸들러는 페이지 테이블을 검색하여 변환 정보를 찾고 TLB를 갱신한 후에 명령어를 재실행합니다.

이 두 가지 방법의 중요한 차이점은 하드웨어 관리 방식은 하드웨어가 직접 TLB 미스를 처리하는 데 반해, 소프트웨어 관리 방식은 운영 체제의 코드가 TLB 미스를 처리한다는 것입니다. 소프트웨어 관리 방식은 유연성이 높고 하드웨어 변경에 영향을 받지 않지만, 성능 측면에서는 하드웨어 관리 방식에 비해 부가적인 오버헤드가 있을 수 있습니다.

TLB 미스가 발생하면 운영 체제의 페이지 폴트 핸들러가 실행되어 페이지 테이블을 참조하여 필요한 가상-물리 주소 변환 정보를 찾는다. 이 정보는 TLB에 로드되고, 이후 메모리 액세스는 TLB 히트로 처리된다.

## TLB의 구성: 무엇이 있나?

TLB는 일반적으로 다음과 같은 항목으로 구성됩니다.

| 항목                            | 설명                                                           |
| ------------------------------- | -------------------------------------------------------------- |
| 가상 페이지 번호(VPN)           | 가상 주소에서 추출된 페이지 번호입니다.                        |
| 물리 페이지 프레임 번호(PPFN)   | 물리 메모리에서 해당 페이지를 저장하는 프레임 번호입니다.      |
| 액세스 권한                     | 해당 페이지에 대한 액세스 권한(읽기, 쓰기, 실행)을 나타냅니다. |
| 유효 비트                       | 해당 엔트리가 유효한지 여부를 나타냅니다.                      |
| ASID (Address Space Identifier) | 프로세스 간 TLB 엔트리 충돌을 방지합니다.                      |

## TLB의 문제: 문맥 교환

다른 프로세스는 다른 가상 주소 공간을 사용하기 때문에 문맥 교환이 발생하면 현재 프로세스의 TLB 엔트리가 유효하지 않게 될 수 있습니다.

이 문제를 해결하기 위해 다음과 같은 방법을 사용할 수 있습니다.

- TLB를 비우기: 문맥 교환이 발생할 때마다 TLB를 비웁니다.
- ASID(Address Space Identifier) 사용: TLB 엔트리에 ASID를 추가하여 프로세스별 TLB 엔트리를 구분합니다.

## 이슈: 교체 정책

TLB Miss가 발생하면 TLB에 새 엔트리를 추가해야 합니다. 하지만 TLB는 제한된 크기의 캐시이기 때문에 어떤 엔트리를 교체해야 할지 결정하는 정책이 필요합니다.  
TLB의 교체 정책은 제한된 공간과 성능 요구 사항을 충족하기 위해 매우 중요합니다. 각 정책은 특정 시나리오에서의 성능 최적화를 목표로 합니다.

일반적인 교체 정책으로는 다음과 같은 것들이 있습니다.

- LRU (Least Recently Used): 최근에 사용되지 않은 항목을 먼저 교체합니다.
- FIFO (First In, First Out): 먼저 추가된 항목을 먼저 교체합니다.
- Random Replacement: 무작위 선택을 통한 교체

## 요약

TLB(Translation Lookaside Buffer)은 가상 주소를 물리 주소로 변환하는 과정에서 사용되는 고속 캐시 메모리입니다.
TLB의 주요 목적은 최근에 사용된 가상-물리 주소 변환 정보를 저장하여 변환 속도를 높이는 것입니다.

TLB 미스가 발생했을 때, 즉 TLB에 필요한 변환 정보가 캐시되어 있지 않을 때, 페이지 테이블 워크 과정을 통해 해당 정보를 찾습니다.
이 과정에서 CPU는 메모리에 있는 페이지 테이블을 참조하여 가상 주소를 물리 주소로 변환합니다.

TLB의 구성 요소는 다음과 같습니다.

가상 페이지 번호(VPN): 가상 주소에서 추출된 페이지 번호로, TLB에서 검색에 사용됩니다.
물리 페이지 프레임 번호(PPFN): 해당 가상 페이지 번호에 대응하는 물리 메모리의 페이지 프레임 번호입니다.
액세스 권한: 해당 페이지에 대한 액세스 권한(읽기, 쓰기, 실행)을 나타냅니다.
유효 비트: TLB 엔트리의 유효성을 나타내며, 해당 엔트리가 유효한지 여부를 판단합니다.
ASID (Address Space Identifier): 프로세스 간 TLB 엔트리 충돌을 방지하기 위한 고유 식별자입니다.
문맥 교환 시에는 현재 프로세스의 TLB 엔트리가 유효하지 않게 될 수 있으므로, 이를 해결하기 위해 TLB를 비우거나 ASID를 사용하는 방법이 있습니다.

TLB의 교체 정책은 TLB 미스가 발생했을 때 어떤 엔트리를 교체할지 결정하는 방법을 정의합니다.
일반적으로 사용되는 교체 정책으로는 LRU(Least Recently Used), FIFO(First In, First Out), Random Replacement 등이 있습니다.
이 정책은 제한된 TLB 공간을 최대한 효율적으로 활용하고 성능을 최적화하기 위해 사용됩니다.
