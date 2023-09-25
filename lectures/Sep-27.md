# Code Review: Merge Sort

## Merge Sort Producing a New List

### Solution 1

    static Node merge(Node A, Node B) {
        if (A == null) { return B; }
        if (B == null) { return A; }
        if (A.data < B.data) {
            return new Node(A.data, merge(A.next, B));
        } else {
            return new Node(B.data, merge(A, B.next));
        }
    }

    static Node sort(Node N) {
        int length = Utils.length(N);
        if (length <= 1) {
            if (N == null) {
                return null;
            } else {
                return new Node(N.data, null); // new list returned
            }
        }
        int middle = length / 2;
        Node left_side = Utils.take(N, middle);
        Node right_side = Utils.drop(N, middle);
        Node leftSorted = sort(left_side);
        Node rightSorted = sort(right_side);
        return merge(leftSorted, rightSorted);
    }

### Solution 2

    static Node merge(Node A, Node B) {
        Node first = null;
        if (A == null) {
            if (B == null) {
                return null;
            }
            first = new Node(B.data, null);
            A = A.next;
        } else if (B == null) {
            first = new Node(A.data, null);
            A = A.next;
        } else if (A.data < B.data) {
            first = new Node(A.data, null);
            A = A.next;
        } else {
            first = new Node(B.data, null);
            B = B.next;
        }
        Node curr = first;
        while (A != null && B != null) {
            if (A.data < B.data) {
                Node tmp = new Node(A.data, null);
                curr.next = tmp;
                curr = tmp;
                A = A.next;
            } else {
                Node tmp = new Node(B.data, null);
                curr.next = tmp;
                curr = tmp;
                B = B.next;
            }
        }
        while (A != null) {
            Node tmp = new Node(A.data, null);
            curr.next = tmp;
            curr = tmp;
            A = A.next;
        }
        while (B != null) {
            Node tmp = new Node(B.data, null);
            curr.next = tmp;
            curr = tmp;
            B = B.next;
        }
        return first;
    }

    static Node sort(Node N) {
        if (N.next == null) {
            return N;
        }

        Node curr = N;

        Node half1 = new Node(N.data, N.next);
        Node curr_slow = half1;
        for (int i = 0; curr != null; ++i) {
            curr = curr.next;
            if ((i % 2 == 1) && i != 1) {
                curr_slow.next = new Node(curr_slow.next.data, curr_slow.next.next);
                curr_slow = curr_slow.next;
            }
        }
        Node half2 = new Node(curr_slow.next.data, curr_slow.next.next);
        curr_slow.next = null;

        half1 = sort(half1);
        half2 = sort(half2);

        Node toReturn = merge(half1, half2);
        return toReturn;
    }


## In-place Merge Sort

### Solution 1

    static Node merge_in_place(Node A, Node B) {
        if (A == null) {
            return B;
        }
        if (B == null) {
            return A;
        }
        if (A.data < B.data) {
            A.next = merge_in_place(A.next, B);
            return A;
        }
        else {
            B.next = merge_in_place(A, B.next);
            return B;
        }
    }

    static Node sort_in_place(Node N) {
        if(N == null || N.next == null){
            return N;
        }
        int mid = Utils.length(N) / 2;
        Node middle = Utils.nth_node(N, mid);
        middle.next = null;
        Node left = sort_in_place(N);
        Node right = sort_in_place(Utils.nth_node(N, mid+1));
        return merge_in_place(left, right);
    }

### Solution 2

    static Node merge_in_place(Node A, Node B) {
        Node first = null;
        if (A == null) {
            if (B == null) {
                return null;
            }
            first = B;
            A = A.next;
        } else if (B == null) {
            first = A;
            A = A.next;
        } else if (A.data < B.data) {
            first = A;
            A = A.next;
        } else {
            first = B;
            B = B.next;
        }
        Node curr = first;
        while (A != null && B != null) {
            if (A.data < B.data) {
                curr.next = A;
                curr = A;
                A = A.next;
            } else {
                curr.next = B;
                curr = B;
                B = B.next;
            }
        }
        while (A != null) {
            curr.next = A;
            curr = A;
            A = A.next;
        }
        while (B != null) {
            curr.next = B;
            curr = B;
            B = B.next;
        }
        return first;
    }

    static Node sort_in_place(Node N) {
        if (N.next == null) {
            return N;
        }
        Node curr = N;
        Node curr_slow = N;
        for (int i = 0; curr != null; ++i) {
            curr = curr.next;
            if ((i % 2 == 1) && i != 1) {
                curr_slow = curr_slow.next;
            }
        }
        Node half2 = curr_slow.next;
        curr_slow.next = null;

        Node half1 = sort_in_place(N);
        half2 = sort_in_place(half2);

        Node toReturn = merge_in_place(half1, half2);
        return toReturn;
    }
