# Javascript의 Map 자료형을 Queue처럼 사용하는 방법

코드는 아래와 같다.

Map 자료형의 get, set, delete 연산은 모두 O(1)의 시간 복잡도를 가지고 있음

Map을 상속해 인덱스를 queue의 정보로 추가하고 해당 변수를 조절하는 것을 통해 Queue 처럼 동작하게 할 수 있다.


```javascript

class Queue extends Map {
    constructor() {
        super();
        this.insertionIndex = 0;
        this.removalIndex = 0;
    }

    queue(element) {
        this.set(this.insertionIndex++, element);
    }

    dequeue() {
        const element = this.get(this.removalIndex);
        if (typeof element != 'undefined') {
            this.delete(this.removalIndex++);
        }
        return el;
    }
}

```