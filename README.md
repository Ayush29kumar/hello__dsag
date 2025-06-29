Based on the images provided, here is the full transcription of the programming question.

---

### **Question 16: Platform queries**
**Max. score: 100.00**

You are tasked with building the backend logic for a simplified Q&A platform. Users can add questions, delete them, and vote on them. Questions also decay over time, and the system must support multiple authors and return the top 10 questions posted by any author. The platform must track each question's vote count and content.

**Each question has:**
*   A vote count (initially 0).
*   Some string content.
*   A unique ID assigned in the order of addition.
*   A deleted flag (questions can be logically deleted).

---

### **Class description**
You are provided with the following abstract base class:
```cpp
class StackOverflow {
public:
    int decayTime=0;
    virtual ~StackOverflow() = default;
    virtual int addQuestion(string &content, string &author)=0;
    virtual void deleteQuestion(int id)=0;
    virtual void upVote(int id)=0;
    virtual void downVote(int id)=0;
    virtual pair<string,int> getQuestionById(int id)=0;
    virtual vector<string> getTop10QuestionsByAuthor(string &author)=0;
    explicit StackOverflow(const int decayTime) : decayTime(decayTime) {}
};
```

---

### **Function description**
*   **`int addQuestion(string &content, string &author)`**
    *   Adds a new question with the given content.
    *   Returns the unique ID of the question.
    *   Initially, the question had 0 votes and was not deleted.

*   **`void deleteQuestion(int id)`**
    *   Deletes the question with the given ID.
    *   If it does not exist or has already been deleted, no action is taken.

*   **`void upVote(int id)`**
    *   Increases the vote count of the given question by 1.
    *   If it does not exist or has already been deleted, no action is taken.

*   **`void downVote(int id)`**
    *   Decreases the vote count of the given question by 1.
    *   If it does not exist or has already been deleted, no action is taken.

*   **`Question getQuestionById(int id)`**
    *   Returns the question content and its current vote count.
    *   If it does not exist or has already been deleted, return a Question with content `"null"` and vote 0.

*   **`vector<string> getTop10QuestionsByAuthor(string &author)`**
    *   Return the Top 10 questions based on **Vote Count** written by the given author. If the number of questions written by the author is less than 10, output all his existing questions.

---

### **Task**
Implement a system that:
*   Allows adding, deleting, and voting on questions.
*   Maintains a collection of questions with associated vote counts and textual content.
*   The vote of each question gets decreased by 1 after **every `decayTime` query**.
*   Handles all queries efficiently (up to 10⁵ queries).
*   You must create a class named `sof` that inherits from the base class `StackOverflow`.
*   You should return appropriate responses for each query in the Output format section.
*   For each `getQuestionById` query, return the current state of the question.

**Note:** The timing of decay can be different for each question. Example: `decayTime=2`, question is added at `time=i`, and upvoted at `time=i+1`, so now decay of this question will take place at `i+1+2` (if there is no update to this question, decay takes place at `i+2`). For each question, decay takes place at `Time = Last_Update_Time_Of_That_Question + decayTime`.

---

### **Input format for custom testing**
*   The first line contains an integer `Q` - The number of queries.
*   The next `Q` lines contain the queries in the following format:
    *   `1 content author` // Add a new question, returns its id
    *   `2 id` // Delete question with given id
    *   `3 id` // Upvote question
    *   `4 id` // Downvote question
    *   `5 id` // Print the content and vote for the question
    *   `6 author` // Print the Top 10 questions asked by the given author

---

### **Output format**
*   For each query of type 1, print:
    *   `Question with id: X is added`
*   For each query of type 2, print:
    *   `Question with id: X is deleted`
*   For each query of type 3, print:
    *   `Question with id: X is upvoted`
*   For each query of type 4, print:
    *   `Question with id: X is downvoted`
*   For each query of type 5, print:
    *   `Content: <content>, Vote: <vote>`
*   For each query of type 6, print:
    *   For `min(10, top10.size())` `<content>`

---

### **Constraints**
*   1 ≤ Q ≤ 10⁵
*   1 ≤ Length of content ≤ 100
*   1 ≤ Length of author name ≤ 100
*   All Id values refer to IDs previously returned from the add query.

---

### **Sample Input**
```
12 2
1 BinarySearch Alice
3 0
5 0
1 Polymorphism Bob
3 1
5 1
6 Alice
4 0
5 0
5 1
```

### **Sample Output**
```
Question with id: 0 is added
Question with id: 0 is upvoted
Content: BinarySearch, Vote: 1
Question with id: 1 is added
Question with id: 1 is upvoted
Content: Polymorphism, Vote: 1
BinarySearch
Question with id: 0 is downvoted
Content: BinarySearch, Vote: -1
Content: Polymorphism, Vote: 1
```

---

### **Explanation**

**Given**
```
12 2
1 BinarySearch Alice
3 0
5 0
1 Polymorphism Bob
3 1
5 1
6 Alice
4 0
5 0
5 1
```
The decay time is 2.

| Time | Query | Explanation | Output |
| :--- | :--- | :--- | :--- |
| 1 | `1 BinarySearch Alice` | Add question "BinarySearch" by Alice -> ID 0 is assigned. | `Question with id: 0 is added` |
| 2 | `3 0` | Upvote ID 0 -> vote = 1. | `Question with id: 0 is upvoted` |
| 3 | `3 0` | Upvote again -> vote = 2. | `Question with id: 0 is upvoted` |
| 4 | `5 0` | Fetch ID 0: content = BinarySearch. Decay happens: to downvote ID 0 -> vote = 1. | `Content: BinarySearch, Vote: 1` |
| 5 | `1 Polymorphism Bob` | Add question "Polymorphism" by Bob -> ID 1 is assigned. | `Question with id: 1 is added` |
| 6 | `3 1` | Upvote ID 1 -> vote = 1. | `Question with id: 1 is upvoted` |
| 7 | `5 1` | Fetch ID 1: vote = 1. | `Content: Polymorphism, Vote: 1` |
| 8 | `6 Alice` | Fetch top questions by Alice. | `BinarySearch` |
| 9 | `4 0` | Downvote ID 0 -> vote = -1. Decay happens: to downvote ID 1 -> vote = 0. | `Question with id: 0 is downvoted` |
| 10 | `4 0` | Downvote ID 0 -> vote = -1. | `Question with id: 0 is downvoted` |
| 11 | `5 0` | Fetch ID 0: vote = -1. Decay happens: to downvote ID 0 -> vote = -2. | `Content: BinarySearch, Vote: -1` |
| 12 | `5 1` | Fetch ID 1: vote = 1. | `Content: Polymorphism, Vote: 1` |

**Note:** Your code must be able to print the sample output from the provided sample input. However, your code is run against multiple hidden test cases. Therefore, your code must pass these hidden test cases to solve the problem statement.

**Limits**
*   Time Limit: 1.0 sec(s) for each input file
*   Memory Limit: 256 MB
*   Source Limit: 1024 KB

**Scoring**
*   Score is assigned if any testcase passes

**Allowed Languages**
*   C++17, C#, Go, Java 17, Kotlin, Python 3.8
