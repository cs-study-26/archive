# 🧑🏻‍💻 Red-Black Tree  

- [✅ 레드블랙트리의 정의](#-레드블랙트리의-정의)  
- [✅ 탐색(Search)](#-탐색search)  
- [✅ 삽입(Insert)](#-삽입insert)  
- [✅ 삭제(Delete)](#-삭제delete)  
- [✅ Recoloring & Rebuilding](#-recoloring--rebuilding)  
- [✅ 연산별 시간 복잡도](#-연산별-시간-복잡도)  
- [✅ 실제 사용 예시](#-실제-사용-예시)  
- [✅ 다른 탐색 트리와의 비교](#-다른-탐색-트리와의-비교)  

> [!NOTE]  
> 레드블랙트리(Red-Black Tree)는 **이진 탐색 트리(BST)** 에 색깔과 규칙을 추가해, 항상 **높이를 O(log n)** 로 유지하는 **자기 균형 이진 탐색 트리**다.  
> 탐색, 삽입, 삭제 모두 최악의 경우에도 O(log n) 보장이 목적이다.  


<br>

## ✅ 레드블랙트리의 정의  
![red-black tree](res/red-black%20tree.png)
> [!NOTE]  
> 레드블랙트리는 일반 BST의 구조(왼쪽 < 루트 < 오른쪽)를 그대로 가지면서, 각 노드에 **색 정보(RED/BLACK)** 를 추가하고, 트리 전체가 지켜야 할 **5가지 속성**을 만족시킨다.   

### 💡 Red-Black Tree의 5가지 속성

> [!IMPORTANT]  
> 1. 각 노드는 RED 또는 BLACK 이다.  
> 2. 루트(Root) 노드는 항상 BLACK 이다.  
> 3. 모든 리프(NIL, 외부 null 노드)는 BLACK 으로 간주한다.  
> 4. RED 노드의 자식은 모두 BLACK 이다.  
>    - 즉, RED 노드는 연속해서 두 번 나올 수 없다. (RED–RED 금지)  
> 5. 임의의 노드에서 그 노드의 모든 리프(NIL)까지 가는 모든 단순 경로는 **동일한 수의 BLACK 노드**를 포함한다.  
>    - 이 수를 그 노드의 **black-height**라고 한다.  

> [!TIP]  
> 4번과 5번 속성 덕분에 트리의 높이는 항상 **2log₂(n+1)** 이내로 제한되며, 이 때문에 탐색·삽입·삭제 연산이 모두 O(log n) 안에 끝난다.  

**노드 구성 예시**
```java
    private static final boolean RED   = true;
    private static final boolean BLACK = false;

    private static class Node {
        int     key;
        boolean color;
        Node    left, right, parent;

        Node(int key) {
            this.key   = key;
            this.color = RED;   // 새 노드는 항상 RED로 삽입
        }
    }
```
- 값(data)  
- 색(color): RED 또는 BLACK  
- left / right / parent 포인터 

<br>

## ✅ 탐색(Search)  

> [!NOTE]  
> 레드블랙트리에서 **탐색 알고리즘 자체는 일반 BST 탐색과 완전히 동일**하다.  
> 색 정보는 탐색 과정에 직접 영향을 주지 않고, **균형 유지용 메타데이터**일 뿐이다.  

```java
    private Node search(Node node, int key) {
        while (node != NIL) {
            if      (key < node.key) node = node.left;
            else if (key > node.key) node = node.right;
            else                     return node;
        }
        return NIL;
    }
```

### 💡 탐색 절차
1. 루트에서 시작한다.  
2. 찾고자 하는 값 `x`와 현재 노드의 키 `key`를 비교한다.  
   - `x == key` → 탐색 성공.  
   - `x < key` → 왼쪽 자식으로 이동.  
   - `x > key` → 오른쪽 자식으로 이동.  
3. 리프(NIL)까지 내려가도 값을 찾지 못하면, 트리에 존재하지 않는 것.  

> [!TIP]  
> 균형이 깨진 일반 BST와 달리, 레드블랙트리는 항상 높이가 O(log n)이므로, 이 탐색 과정의 **최악 시간 복잡도는 O(log n)** 이다.  

<br>

## ✅ Recoloring & Rebuilding  

### 💡 Recoloring (색 재도색)

> [!NOTE]  
> Recoloring은 **노드의 색만 바꾸는 연산**이다.  
> 삽입/삭제 후 규칙을 어기는 경우, 회전 없이 색만 바꿔서 속성을 복구할 수 있으면 **가장 단순한 해결책**으로 사용된다.  

주요 역할:

- 삽입 시, 부모와 삼촌이 RED 인 경우:  
  - 부모, 삼촌을 BLACK 으로, 할아버지를 RED 로 변경.  
  - black-height 균형을 유지하면서 RED–RED 위반을 “위로 전달”.  
- 삭제 시, 형제/부모/조카의 색 조합에 따라 double black 해소를 위해 색만 바꾸기도 함.  

### 💡 Rotation (회전)

```java
// ══════════════════════════════════════════════
    //  회전 (Rotation)
    // ══════════════════════════════════════════════

    /**
     * 왼쪽 회전 (Left Rotation)
     *
     *     x                y
     *    / \              / \
     *   A   y    →→→    x   C
     *      / \         / \
     *     B   C       A   B
     */
    private void rotateLeft(Node x) {
        Node y = x.right;       // y = x의 오른쪽 자식

        // 1) y의 왼쪽 서브트리(B)를 x의 오른쪽으로
        x.right = y.left;
        if (y.left != NIL) y.left.parent = x;

        // 2) y가 x의 자리를 대체
        y.parent = x.parent;
        if      (x.parent == NIL)        root          = y;
        else if (x == x.parent.left)     x.parent.left = y;
        else                             x.parent.right= y;

        // 3) x를 y의 왼쪽 자식으로
        y.left   = x;
        x.parent = y;
    }

    /**
     * 오른쪽 회전 (Right Rotation) — rotateLeft의 좌우 대칭
     *
     *       y              x
     *      / \            / \
     *     x   C   →→→   A   y
     *    / \                / \
     *   A   B              B   C
     */
    private void rotateRight(Node y) {
        Node x = y.left;

        y.left = x.right;
        if (x.right != NIL) x.right.parent = y;

        x.parent = y.parent;
        if      (y.parent == NIL)        root          = x;
        else if (y == y.parent.right)    y.parent.right= x;
        else                             y.parent.left = x;

        x.right  = y;
        y.parent = x;
    }
```


> [!NOTE]  
> Rotation은 **트리의 구조를 변경하는 연산**으로, BST 순서는 유지하면서 **부모–자식 관계를 바꾸는 연산**이다.  
> - Left Rotation (좌회전): 오른쪽 자식을 위로 올리고, 현재 노드를 왼쪽 자식으로 내린다.  
> - Right Rotation (우회전): 왼쪽 자식을 위로 올리고, 현재 노드를 오른쪽 자식으로 내린다.  

Rotation은:

- 트리의 **높이와 형태를 조절**해 균형을 맞추고,  
- Recoloring과 함께 사용되어 **RED–BLACK 규칙과 black-height를 동시에 만족**하도록 만든다.  

### 💡 Rebuilding = Recoloring + Rotation

> [!IMPORTANT]  
> 삽입/삭제 후 균형이 깨졌을 때, 단순 recoloring으로 해결되지 않는 케이스에서는 **회전 + recoloring**을 조합해 구조를 다시 만드는 과정을 통틀어 Rebuilding이라고 볼 수 있다.  

예:

- 삽입 시, “부모 RED, 삼촌 BLACK, 새 노드가 부모의 ‘바깥쪽’에 위치” → 1~2회 rotation + 색 교환  
- 삭제 시, 형제 쪽 서브트리에 RED 자식이 있을 때 → 그 자식이 바깥쪽이 되도록 1회 rotation, 이후 1회 rotation + recoloring으로 double black 해소  

> [!TIP]  
> 각 노드에서 Rebuilding이 일어나도, 그 깊이는 트리 높이에 비례하기 때문에 전체 비용은 **O(log n)** 이다.  

<br>

## ✅ 삽입(Insert)  

> [!NOTE]  
> 삽입은 크게 두 단계로 나눌 수 있다.  
> 1. **BST 규칙에 따라 새 노드를 삽입**  
> 2. 색 재도색과 회전을 수행해 **레드블랙트리 속성을 복구** 

```java
    // ══════════════════════════════════════════════
    //  INSERT
    // ══════════════════════════════════════════════

    public void insert(int key) {
        Node z = new Node(key);
        z.left   = NIL;
        z.right  = NIL;
        z.parent = NIL;

        // 1) 일반 BST 삽입
        Node y = NIL;
        Node x = root;
        while (x != NIL) {
            y = x;
            if      (z.key < x.key) x = x.left;
            else if (z.key > x.key) x = x.right;
            else return;            // 중복 키 무시
        }
        z.parent = y;
        if      (y == NIL)       root    = z;
        else if (z.key < y.key)  y.left  = z;
        else                     y.right = z;

        // 2) RB 속성 복구
        insertFixup(z);
    }

    /**
     * 삽입 후 RED–RED 위반을 수정한다.
     *
     * 위반 가능성: z(RED)의 부모 p(RED) → 연속 RED
     *
     * 총 6가지 케이스 (왼쪽 3 + 오른쪽 3, 좌우 대칭)
     */
    private void insertFixup(Node z) {
        while (isRed(z.parent)) {
            Node p = z.parent;          // 부모
            Node g = p.parent;          // 할아버지

            if (p == g.left) {
                // ── 부모가 할아버지의 왼쪽 자식 ──
                Node u = g.right;       // 삼촌

                if (isRed(u)) {
                    // ────────────────────────────────
                    // Case 1: 삼촌이 RED → Recoloring
                    // ────────────────────────────────
                    //
                    //       G(B)                G(R)  ← 할아버지를 RED로
                    //      /    \              /    \
                    //    P(R)  U(R)   →→→   P(B)  U(B)  ← 부모·삼촌을 BLACK
                    //    /                  /
                    //  Z(R)               Z(R)
                    //
                    // 검정 노드 수 변화 없음.
                    // 할아버지가 RED가 됐으므로 위쪽에서 또 위반 가능 → 루프 반복
                    p.color = BLACK;
                    u.color = BLACK;
                    g.color = RED;
                    z = g;              // 할아버지로 올라가서 재검사

                } else {
                    if (z == p.right) {
                        // ────────────────────────────────
                        // Case 2 (LR): 내가 부모의 오른쪽 → 왼쪽 회전으로 Case 3으로 변환
                        // ────────────────────────────────
                        //
                        //     G(B)              G(B)
                        //    /    \            /    \
                        //  P(R)  U(B)  →→→  Z(R)  U(B)
                        //     \             /
                        //     Z(R)        P(R)
                        //
                        z = p;
                        rotateLeft(z);
                        p = z.parent;   // 회전 후 포인터 갱신
                        g = p.parent;
                    }
                    // ────────────────────────────────
                    // Case 3 (LL): 내가 부모의 왼쪽 → 오른쪽 회전 + 색 교환
                    // ────────────────────────────────
                    //
                    //       G(B)               P(B)
                    //      /    \             /    \
                    //    P(R)  U(B)  →→→   Z(R)  G(R)
                    //    /                           \
                    //  Z(R)                          U(B)
                    //
                    p.color = BLACK;
                    g.color = RED;
                    rotateRight(g);
                }

            } else {
                // ── 부모가 할아버지의 오른쪽 자식 (왼쪽의 좌우 대칭) ──
                Node u = g.left;        // 삼촌

                if (isRed(u)) {
                    // Case 1 (대칭): Recoloring
                    p.color = BLACK;
                    u.color = BLACK;
                    g.color = RED;
                    z = g;

                } else {
                    if (z == p.left) {
                        // Case 2 (RL): 오른쪽 회전으로 Case 3으로 변환
                        z = p;
                        rotateRight(z);
                        p = z.parent;
                        g = p.parent;
                    }
                    // Case 3 (RR): 왼쪽 회전 + 색 교환
                    p.color = BLACK;
                    g.color = RED;
                    rotateLeft(g);
                }
            }
        }
        root.color = BLACK;     // 루트는 항상 BLACK (Rule 2)
    }
```

### 💡 1단계: BST 삽입

- 새 노드를 **일반 BST 삽입 규칙**으로 알맞은 위치에 추가한다.  
- 새 노드는 보통 **RED** 로 삽입한다.  
  - 이유: 새 노드를 BLACK 으로 두면 해당 경로의 black-height가 증가해 5번 속성을 깨기 쉽기 때문이다.  

### 💡 2단계: 속성 위반 여부 확인

삽입 후 다음 상황이 문제가 된다.

- 새 노드가 RED 이고, 그 **부모도 RED** 인 경우  
  - → 4번 속성(RED 노드의 자식은 BLACK)을 위반 (RED–RED 위반)  

이때, **부모의 형제(삼촌, uncle)의 색**에 따라 두 가지 큰 케이스로 나뉜다.

#### 케이스 A: 삼촌이 RED (Recoloring 중심)

- 새 노드의 부모(P)와 삼촌(U)이 모두 RED, 할아버지(G)는 BLACK.  
- 해결 전략: **색을 올려 보내는 방식의 recoloring**  
  - P와 U를 BLACK 으로 바꾸고, G를 RED 로 바꾼다.  
  - 이제 G가 RED 가 되었으므로, G를 새 노드처럼 보고 **위로 올라가며 같은 과정을 반복**한다.  
- 회전은 필요 없고, **색만 바꾸는 단계**다.  

#### 케이스 B: 삼촌이 BLACK 또는 NIL (Rotate + Recolor)

- 부모(P)는 RED, 삼촌(U)은 BLACK(or NIL).  
- 새 노드의 위치(왼왼, 왼오, 오른왼, 오른오)에 따라 다음 작업을 한다.  
  - 1차 회전(트리를 LL 또는 RR 형태로 맞추기 위한 rotate)  
  - 2차 회전(실제 균형 회복용 rotate)  
  - 회전 후, P와 G의 색을 서로 바꿔 **RED–RED 위반과 black-height 문제를 해결**  

예) 새 노드가 “오른쪽-왼쪽” 형태에 삽입된 경우  
- 먼저 부모 기준으로 **오른쪽 회전 또는 왼쪽 회전**을 하여 “왼왼” 또는 “오른오른” 형태로 맞춘 뒤,  
- 할아버지를 기준으로 반대 방향 회전 + recoloring을 수행한다.  

> [!TIP]  
> 삽입 시 회전은 최대 2번, recoloring은 경로를 따라 O(log n) 깊이에서도 수행 가능하지만, 전체 연산은 항상 트리 높이에 비례하므로 **O(log n)** 이다.  

<br>

## ✅ 삭제(Delete)  

> [!NOTE]  
> 삭제는 삽입보다 복잡하지만, 기본 아이디어는  
> 1. BST 규칙으로 노드를 제거  
> 2. 제거로 인해 **black-height 또는 RED–BLACK 규칙이 깨졌는지 확인**  
> 3. recoloring + 회전으로 복구  
> 로 요약할 수 있다.  

```java
    // ══════════════════════════════════════════════
    //  DELETE
    // ══════════════════════════════════════════════

    public void delete(int key) {
        Node z = search(root, key);
        if (z == NIL) return;   // 없는 키면 무시
        deleteNode(z);
    }

    /**
     * 노드 z를 트리에서 제거한다.
     *
     * BST 삭제와 동일하게 세 가지 경우:
     *   1) 왼쪽 자식 없음 → 오른쪽 자식으로 대체
     *   2) 오른쪽 자식 없음 → 왼쪽 자식으로 대체
     *   3) 자식 둘 다 있음 → 오른쪽 서브트리의 최솟값(successor)으로 대체
     */
    private void deleteNode(Node z) {
        Node y = z;                     // 실제로 트리에서 빠질 노드
        Node x;                         // y가 빠진 자리를 채울 노드
        boolean yOriginalColor = y.color;

        if (z.left == NIL) {
            // Case A: 왼쪽 자식 없음
            x = z.right;
            transplant(z, z.right);

        } else if (z.right == NIL) {
            // Case B: 오른쪽 자식 없음
            x = z.left;
            transplant(z, z.left);

        } else {
            // Case C: 자식 둘 다 있음 → successor(오른쪽 서브트리 최솟값)
            y = minimum(z.right);
            yOriginalColor = y.color;
            x = y.right;

            if (y.parent == z) {
                // y가 z의 바로 오른쪽 자식인 경우
                x.parent = y;           // x가 NIL이어도 parent 설정
            } else {
                // y를 y.right로 대체하고, y를 z.right의 루트로 올림
                transplant(y, y.right);
                y.right        = z.right;
                y.right.parent = y;
            }

            transplant(z, y);
            y.left        = z.left;
            y.left.parent = y;
            y.color       = z.color;   // z의 색을 그대로 물려받음
        }

        // 실제로 빠진 노드(y)가 BLACK이면 RB 속성이 깨질 수 있다
        // → "x에 여분의 BLACK이 씌워진 것"으로 간주하고 복구
        if (yOriginalColor == BLACK) {
            deleteFixup(x);
        }
    }

    /**
     * u 자리를 v로 대체 (부모 포인터만 수정, 자식은 호출자가 처리)
     */
    private void transplant(Node u, Node v) {
        if      (u.parent == NIL)       root          = v;
        else if (u == u.parent.left)    u.parent.left = v;
        else                            u.parent.right= v;
        v.parent = u.parent;
    }

    /**
     * 삭제 후 BLACK 노드 수 불균형을 수정한다.
     *
     * x는 "여분의 BLACK"을 가진 노드.
     * x가 RED이거나 루트면 그냥 BLACK으로 칠하면 끝.
     * x가 BLACK이면 4가지 케이스로 나눠 처리.
     *
     * 총 8가지 케이스 (왼쪽 4 + 오른쪽 4, 좌우 대칭)
     */
    private void deleteFixup(Node x) {
        while (x != root && isBlack(x)) {
            Node p = x.parent;

            if (x == p.left) {
                // ── x가 부모의 왼쪽 자식 ──
                Node w = p.right;       // 형제(sibling)

                // ────────────────────────────────
                // Case 1: 형제 w가 RED
                // ────────────────────────────────
                //   → w를 BLACK, p를 RED로 바꾸고 p 기준 왼쪽 회전
                //   → w가 BLACK인 Case 2~4로 변환
                //
                //      P(R 또는 B)              W(B)
                //     /           \            /    \
                //   X(B+B)       W(R)  →→→  P(R)  D(B)
                //               /  \        / \
                //             C(B) D(B)  X(B+B) C(B)
                //                              ↑ 이제 새 형제는 C(B)
                //
                if (isRed(w)) {
                    w.color = BLACK;
                    p.color = RED;
                    rotateLeft(p);
                    w = p.right;        // 새 형제로 갱신
                }

                if (isBlack(w.left) && isBlack(w.right)) {
                    // ────────────────────────────────
                    // Case 2: 형제 w가 BLACK, w의 자식 둘 다 BLACK
                    // ────────────────────────────────
                    //   → w를 RED로 바꿔 "여분의 BLACK"을 부모로 전파
                    //
                    //       P(?)             P(? + 1 extra BLACK)
                    //      /    \           /    \
                    //   X(B+B) W(B)  →→→  X(B)  W(R)
                    //         /  \               /  \
                    //       C(B) D(B)          C(B) D(B)
                    //
                    w.color = RED;
                    x = p;              // 여분의 BLACK을 위로 올림

                } else {
                    if (isBlack(w.right)) {
                        // ────────────────────────────────
                        // Case 3: 형제 w가 BLACK, w의 오른쪽 자식 BLACK, 왼쪽 자식 RED
                        // ────────────────────────────────
                        //   → w와 w.left 색 교환 + w 기준 오른쪽 회전 → Case 4로 변환
                        //
                        //       P(?)                  P(?)
                        //      /    \                /    \
                        //   X(B+B) W(B)    →→→   X(B+B) C(B)  ← 새 형제
                        //         /  \                      \
                        //       C(R) D(B)                  W(R)
                        //                                     \
                        //                                     D(B)
                        //
                        w.left.color = BLACK;
                        w.color      = RED;
                        rotateRight(w);
                        w = p.right;
                    }
                    // ────────────────────────────────
                    // Case 4: 형제 w가 BLACK, w의 오른쪽 자식 RED
                    // ────────────────────────────────
                    //   → p의 색을 w에 이식, p·w.right를 BLACK으로, p 기준 왼쪽 회전
                    //   → 여분의 BLACK이 해소되어 루프 종료
                    //
                    //       P(c)                  W(c)
                    //      /    \                /    \
                    //   X(B+B) W(B)    →→→    P(B)  D(B)
                    //         /  \           /   \
                    //       C(?) D(R)      X(B)  C(?)
                    //
                    w.color       = p.color;
                    p.color       = BLACK;
                    w.right.color = BLACK;
                    rotateLeft(p);
                    x = root;           // 루프 강제 종료
                }

            } else {
                // ── x가 부모의 오른쪽 자식 (왼쪽의 좌우 대칭) ──
                Node w = p.left;

                // Case 1 (대칭)
                if (isRed(w)) {
                    w.color = BLACK;
                    p.color = RED;
                    rotateRight(p);
                    w = p.left;
                }

                if (isBlack(w.right) && isBlack(w.left)) {
                    // Case 2 (대칭)
                    w.color = RED;
                    x = p;

                } else {
                    if (isBlack(w.left)) {
                        // Case 3 (대칭)
                        w.right.color = BLACK;
                        w.color       = RED;
                        rotateLeft(w);
                        w = p.left;
                    }
                    // Case 4 (대칭)
                    w.color      = p.color;
                    p.color      = BLACK;
                    w.left.color = BLACK;
                    rotateRight(p);
                    x = root;
                }
            }
        }
        x.color = BLACK;    // x가 RED였으면 그냥 BLACK으로 (여분의 BLACK 흡수)
    }
```

### 💡 1단계: BST 삭제

- 삭제 대상 노드 `z`를 찾는다.  
- 세 가지 경우로 나뉜다.  
  1. 자식이 0개 (리프): 바로 제거.  
  2. 자식이 1개: 자식을 `z` 위치로 올리고 `z` 제거.  
  3. 자식이 2개:  
     - 보통 **중위 후속자(successor)** 를 찾아 `z`와 값을 교환한 뒤,  
     - 1번 또는 2번 경우로 감소시켜 처리한다.  

### 💡 2단계: 색/구조 위반 복구

> [!NOTE]  
> 삭제되는 노드나 그 자리가 **BLACK** 인 경우, 5번 속성(black-height 등)이 깨질 수 있다.  
> 이를 보정하기 위해 **“double black”** 개념을 쓰기도 한다.  

주요 아이디어:

- 삭제 후, 어떤 서브트리 루트가 **BLACK을 하나 잃었다**고 보고, 이를 “double black” 상태로 표시한다.  
- 형제(sibling)의 색과 형제의 자식 색에 따라 여러 케이스로 나뉜다.  

대표적인 패턴 몇 가지:

1. **형제가 RED**:  
   - 부모는 BLACK, 형제는 RED → 일단 회전(부모 기준) + 색 교환으로 형제를 BLACK으로 만들고,  
   - 이후 다른 케이스로 전환해 처리.  

2. **형제가 BLACK이고, 형제의 자식들도 모두 BLACK**:  
   - 형제를 RED로 바꾸고, “double black”을 부모로 위로 올린다.  
   - 부모가 RED였으면 BLACK으로 바뀌면서 종료될 수 있고, 부모가 BLACK이면 다시 double black 처리 반복.  

3. **형제가 BLACK이고, 형제의 자식 중 하나가 RED**:  
   - RED 자식이 어느 쪽에 있는지에 따라 **1~2회 회전 + recoloring**으로 double black 해소.  

> [!TIP]  
> 삭제의 케이스들은 많지만, 공통적으로 “black-height를 맞추기 위해 **형제와 부모, 형제 자식의 색/구조를 조정**한다”라고 이해하면 된다.  
> 이 과정 역시 트리 높이에 비례하므로 **O(log n)** 시간에 끝난다.  

<br>

## ✅ 연산별 시간 복잡도  

레드블랙트리는 **트리 높이가 항상 O(log n)** 라는 점이 핵심이다.

| 연산         | 시간 복잡도 (최악) | 설명 |
|--------------|-------------------|------|
| 탐색(Search) | O(log n)     | BST 방식으로 높이만큼만 내려간다. |
| 삽입(Insert) | O(log n)     | BST 삽입 O(log n) + Rebuilding (recolor + ≤2회 회전). |
| 삭제(Delete) | O(log n)     | BST 삭제 O(log n) + Rebuilding (여러 케이스 처리). |
| 최소/최대    | O(log n)     | 가장 왼쪽/오른쪽으로 내려간다. |

> [!NOTE]  
> Recoloring 자체는 한 번에 O(1)이고, rotation 역시 O(1) 이지만, **최대 트리 높이만큼 위로 전파**될 수 있기 때문에 전체 연산은 O(log n)으로 본다.  

<br>

## ✅ 실제 사용 예시  

> [!NOTE]  
> 레드블랙트리는 **“정렬된 상태를 유지하면서 탐색/삽입/삭제가 자주 일어나는”** 경우에 매우 적합하다.  

대표적인 사용처:

- **언어 표준 라이브러리 컨테이너**  
  - Java: `TreeMap`, `TreeSet` 내부 구현  
  - C++: `std::map`, `std::set` (대부분 레드블랙트리 또는 유사 구조)  
- **심볼 테이블(Symbol Table)**  
  - 컴파일러에서 변수/함수 이름 → 주소/타입 매핑 관리  
- **이벤트 스케줄링 / 시뮬레이션**  
  - “시간 순서대로 처리해야 하는 이벤트”를 키로 갖는 균형 트리  
- **운영체제, 네트워크 스케줄러, 커널 내부 자료구조**  
  - 예: Process 관리, 타이머 관리, interval tree 기반 기능 등에서 기본 블록으로 활용  

> [!TIP]  
> 실제로 “정렬된 맵이나 셋”을 쓰면, 내부적으로 레드블랙트리 혹은 비슷한 자기 균형 BST가 거의 항상 돌아가고 있다고 보면 된다.  

<br>

## ✅ 다른 탐색 트리와의 비교  

### 💡 시간 복잡도 비교

| 자료구조                 | 탐색 | 삽입 | 삭제 | 비고 |
|--------------------------|------|------|------|------|
| **일반 BST**            | 평균 O(log n), 최악 O(n) | 평균 O(log n), 최악 O(n) | 평균 O(log n), 최악 O(n) | 균형 유지 X, 삽입 순서에 민감 |
| **Red-Black 트리**        | O(log n) | O(log n) | O(log n) | 느슨한 균형, 회전 적음 |
| **AVL 트리**             | O(log n) | O(log n) | O(log n) | 더 엄격한 균형, 탐색에 유리, 삽입/삭제 시 회전 많음 |
| **B-Tree / B+Tree**     | O(log n) | O(log n) | O(log n) | 디스크/DB·파일시스템에 최적화 (다수의 키/자식) |

> [!NOTE]  
> - **레드블랙 vs 일반 BST**  
>   - 일반 BST는 최악의 경우 한쪽으로 쏠려 **연결 리스트**가 되어 O(n) 까지 느려질 수 있음.  
>   - 레드블랙은 항상 O(log n) 유지 → 일관된 성능이 필요한 실무에서 선호.  
> - **레드블랙 vs AVL**  
>   - 둘 다 이론상 시간 복잡도는 동일하지만,  
>   - AVL은 “균형 조건 더 빡셈” → 탐색 약간 더 빠를 수 있고, 삽입/삭제 때 회전 많이 필요.  
>   - 레드블랙은 “균형 조건 느슨” → 삽입/삭제 회전 적고, 구현이 표준 라이브러리 친화적.  