# Chapter 10. 클래스

- [Chapter 10. 클래스](#chapter-10-클래스)
  - [클래스 체계](#클래스-체계)
    - [캡슐화](#캡슐화)
  - [클래스는 작아야 한다](#클래스는-작아야-한다)
    - [단일 책임의 원칙](#단일-책임의-원칙)
    - [응집도(Cohesion)](#응집도cohesion)
      - [응집도를 유지하면 작은 클래스 여럿이 나온다](#응집도를-유지하면-작은-클래스-여럿이-나온다)
  - [변경하기 쉬운 클래스](#변경하기-쉬운-클래스)
    - [변경으로부터 격리](#변경으로부터-격리)
---

## 클래스 체계

- public 변수가 필요한 경우는 거의 없다.
- private 함수는 자신을 호출하는 public 함수 직후에 넣는다. 즉, 추상화 단계가 순차적으로 내려간다.
- 그래서 프로그램은 신문처럼 읽힌다.

### 캡슐화

- 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙도 없다.
- 때로는 변수나 유틸리티 함수를 protected로 선언해 테스트 코드에 접근을 허용하기도 한다.
- 같은 패키지 안에서 테스트 코드가 함수를 호출하거나 변수를 사용해야 한다면 그 함수나 변수를 protected로 선언하거나 패키지 전체로 공개한다.
- 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

## 클래스는 작아야 한다

- 클래스를 설계할 때도, 함수와 마찬가지로, '작게'가 기본 규칙이다.
- 그렇다면 '얼마나 작아야 할까?'
- 함수는 물리적인 행 수로 크기를 측정했다. 클래스는 다른 척도를 사용한다.
- 클래스가 맡은 책임을 센다.

```java
// 10-1
// 너무 많은 책임
public class SuperDashboard extends JFrame implements MetaDataUser {
    public String getCustomizerLanguagePath()
    public void setSystemConfigPath(String systemConfigPath) 
    public String getSystemConfigDocument()
    public void setSystemConfigDocument(String systemConfigDocument) 
    public boolean getGuruState()
    public boolean getNoviceState()
    public boolean getOpenSourceState()
    public void showObject(MetaObject object) 
    public void showProgress(String s)
    public boolean isMetadataDirty()
    public void setIsMetadataDirty(boolean isMetadataDirty)
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public void setMouseSelectState(boolean isMouseSelected) 
    public boolean isMouseSelected()
    public LanguageManager getLanguageManager()
    public Project getProject()
    public Project getFirstProject()
    public Project getLastProject()
    public String getNewProjectName()
    public void setComponentSizes(Dimension dim)
    public String getCurrentDir()
    public void setCurrentDir(String newDir)
    public void updateStatus(int dotPos, int markPos)
    public Class[] getDataBaseClasses()
    public MetadataFeeder getMetadataFeeder()
    public void addProject(Project project)
    public boolean setCurrentProject(Project project)
    public boolean removeProject(Project project)
    public MetaProjectHeader getProgramMetadata()
    public void resetDashboard()
    public Project loadProject(String fileName, String projectName)
    public void setCanSaveMetadata(boolean canSave)
    public MetaObject getSelectedObject()
    public void deselectObjects()
    public void setProject(Project project)
    public void editorAction(String actionName, ActionEvent event) 
    public void setMode(int mode)
    public FileManager getFileManager()
    public void setFileManager(FileManager fileManager)
    public ConfigManager getConfigManager()
    public void setConfigManager(ConfigManager configManager) 
    public ClassLoader getClassLoader()
    public void setClassLoader(ClassLoader classLoader)
    public Properties getProps()
    public String getUserHome()
    public String getBaseDir()
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber()
    public MetaObject pasting(MetaObject target, MetaObject pasted, MetaProject project)
    public void processMenuItems(MetaObject metaObject)
    public void processMenuSeparators(MetaObject metaObject) 
    public void processTabPages(MetaObject metaObject)
    public void processPlacement(MetaObject object)
    public void processCreateLayout(MetaObject object)
    public void updateDisplayLayer(MetaObject object, int layerIndex) 
    public void propertyEditedRepaint(MetaObject object)
    public void processDeleteObject(MetaObject object)
    public boolean getAttachedToDesigner()
    public void processProjectChangedState(boolean hasProjectChanged) 
    public void processObjectNameChanged(MetaObject object)
    public void runProject()
    public void setAçowDragging(boolean allowDragging) 
    public boolean allowDragging()
    public boolean isCustomizing()
    public void setTitle(String title)
    public IdeMenuBar getIdeMenuBar()
    public void showHelper(MetaObject metaObject, String propertyName) 

    // ... many non-public methods follow ...
}
```

- 만약 SuperDashboard가 목록 10-2와 같이 메서드 몇 개만 포함한다면?

```java
// 10-2
// 충분히 작을까?
public class SuperDashboard extends JFrame implements MetaDataUser {
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber() 
}
```

- 메서드 다섯 개 정도면 괜찮을 줄 알았으나, 여기선 아니다.
- SuperDashboard는 메서드 수가 작음에도 불구하고 책임이 너무 많다.
- 클래스 이름은 해당 클래스 책임을 기술해야 한다.
- 네이밍은 클래스 크기를 줄이는 첫 번째 관문이다.
- 간결한 이름이 떠오르지 않는다면 클래스 크기가 너무 커서 그렇다.
- 예를들어, 클래스 이름에 Processor, Manager, Super 등과 같이 모호한 단어가 있다면 클래스에다 여러 책임을 떠안겼다는 증거다.
- 또한 클래스 설명은 if, and, or, but을 사용하지 않고서 25단어 내외로 가능해야 한다.

> "superDashboard는 마지막으로 포커스를 얻었던 컴포넌트에 접근하는 방법을 제공하며, 버전과 빌드 번호를 추적하는 메커니즘을 제공한다."

SuperDashboard에 책임이 너무 많다는 증거다.

### 단일 책임의 원칙

> Single Responsibility Principle, SRP

- 클래스나 모듈을 변경할 이유가 하나뿐이어야 한다는 원칙이다.
- 10-2의 SuperDashboard를 변경할 이유가 두 가지다.
  1. SuperDashboard는 소프트웨어 버전 정보를 추적한다. 그런데, 버전 정보는 소프트웨어를 출시할 때마다 달라진다.
  2. SuperDashboard는 자바 스윙 컴포넌트를 관리한다. 즉, 스윙 코드를 변경할 때마다 버전 번호가 달라진다.

- 책임, 즉 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워진다. 더 좋은 추상화가 더 쉽게 떠오른다.

- SuperDashboard에서 버전 정보를 다루는 메서드 세 개를 따로 빼내 Version이라는 독자적인 클래스를 만든다.

```java
// 10-3
// 단일 책임 클래스
public class Version {
    public int getMajorVersionNumber();
	public int getMinorVersionNumber();
    public int getBuildNumber();
}
```

- SRP는 객체 지향 설계에서 더욱 중요한 개념이다. **하지만 SRP는 클래스 설계자가 가장 무시하는 규칙 중 하나**다.
- 우리는 수많은 책임을 떠안은 클래스를 꾸준하게 접한다. 왜일까?
- 소프트웨어를 돌아가게 만드는 활동과 소프트웨어를 깨끗하게 만드는 활동은 완전히 별개다.
- 우리 대다수는 '깨끗하고 체계적인 소프트웨어'보다 '돌아가는 소프트웨어'에 초점을 맞춘다. 이와 같은 관심사 분리는 중요하다.
- 문제는 **우리들 대다수가 프로그램이 돌아가면 일이 끝났다고 여기는 데 있다.**

</br>

- 규모가 어느 수준에 이르는 시스템은 논리가 많고도 복잡하다. 이런 복잡성을 다루려면 체계적인 정리가 필수다.
- 그래야 변경을 할 때 직접 영향이 미치는 컴포넌트가 어디인지 알 수 있다.

- 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.

- 작은 클래스는 각자 **맡은 책임이 하나**며, **변경할 이유가 하나**며, 다른 작은 클래스와 협력해 시스템에 필요한 동작을 수행한다.

### 응집도(Cohesion)

> 응집도가 높다는 말은 **클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다**는 의미이다.

- 클래스는 인스턴스 변수 수가 작아야 한다.
- 각 클래스 메서드는 인스턴스 변수를 하나 이상 사용해야 한다.
- 일반적으로 **메서드가 변수를 더 많이 사용 할수록 메서드와 클래스는 응집도가 더 높다**.
- 일반적으로 이처럼 응집도가 가장 높은 클래스는 가능하지도 바람직하지도 않다. 그렇지만 우리는 응집도가 높은 클래스를 선호한다.

```java
// 10-4 응집도가 높은 클래스
// Stack.java
public class Stack {
    private int topOfStack = 0;
    List<Integer> elements = new LinkedList<Integer>();

    public int size() { 
        return topOfStack;
    }

    public void push(int element) { 
        topOfStack++; 
        elements.add(element);
    }

    public int pop() throws PoppedWhenEmpty { 
        if (topOfStack == 0)
            throw new PoppedWhenEmpty();
        int element = elements.get(--topOfStack); 
        elements.remove(topOfStack);
        return element;
    }
}
```

- `size()`를 제외한 다른 두 메서드는 두 변수를 모두 사용하므로 응집도가 아주 높다.

</br>

- '함수를 작게, 매개변수 목록을 짧게'라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다. 이는 **새로운 클래스로 쪼개야 한다는 신호**다.

- 응집도가 높아지도록 변수와 메서드를 적 절히 분리해 새로운 클래스 두세 개로 쪼개준다.

#### 응집도를 유지하면 작은 클래스 여럿이 나온다

> 큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스 여럿으로 쪼갤 기회가 생긴다

- 큰 함수를 작은 함수 여럿으로 나누기만 해도 클래스 수가 많아진다.

- 예를 들어보자

  - 변수가 아주 많은 큰 함수가 하나 있다.
  - 큰 함수 일부를 작은 함수 하나로 빼내고 싶은데, 빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다.
  - 그렇다면, 변수 네 개를 새 함수에 인수로 넘겨야 옳을까?
  - 만약, **네 변수를 클래스 인스턴스 변수로 승격한다면 새 함수는 인수가 필요 없다**. 그만큼 함수를 쪼개기 쉬워진다.

  ```java
  // 변수의 종류
  public class ExampleClass {
      int instanceVariable; // 인스턴스 변수
      static int classVariable; // 클래스 변수
      void bigMethod() {
          int localVariable; //지역 변수
          classVariable = 3;
      }
  }
  ```



```java
// 10-5
// PrintPrimes.java
package literatePrimes;

public class PrintPrimes {
    public static void main(String[] args) {
        final int M = 1000; 
        final int RR = 50;
        final int CC = 4;
        final int WW = 10;
        final int ORDMAX = 30; 
        int P[] = new int[M + 1]; 
        int PAGENUMBER;
        int PAGEOFFSET; 
        int ROWOFFSET; 
        int C;
        int J;
        int K;
        boolean JPRIME;
        int ORD;
        int SQUARE;
        int N;
        int MULT[] = new int[ORDMAX + 1];

        J = 1;
        K = 1; 
        P[1] = 2; 
        ORD = 2; 
        SQUARE = 9;

        while (K < M) { 
            do {
                J = J + 2;
                if (J == SQUARE) {
                    ORD = ORD + 1;
                    SQUARE = P[ORD] * P[ORD]; 
                    MULT[ORD - 1] = J;
                }
                N = 2;
                JPRIME = true;
                while (N < ORD && JPRIME) {
                    while (MULT[N] < J)
                        MULT[N] = MULT[N] + P[N] + P[N];
                    if (MULT[N] == J) 
                        JPRIME = false;
                    N = N + 1; 
                }
            } while (!JPRIME); 
            K = K + 1;
            P[K] = J;
        } 
        {
            PAGENUMBER = 1; 
            PAGEOFFSET = 1;
            while (PAGEOFFSET <= M) {
                System.out.println("The First " + M + " Prime Numbers --- Page " + PAGENUMBER);
                System.out.println("");
                for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++) {
                    for (C = 0; C < CC;C++)
                        if (ROWOFFSET + C * RR <= M)
                            System.out.format("%10d", P[ROWOFFSET + C * RR]); 
                    System.out.println("");
                }
                System.out.println("\f"); PAGENUMBER = PAGENUMBER + 1; PAGEOFFSET = PAGEOFFSET + RR * CC;
            }
        }
    }
}
```

- 함수가 하나뿐인 위 프로그램은 엉망진창이다.
  - 들여쓰기가 심하고, 이상한 변수가 많고, 구조가 빡빡하게 결합되었다.
  - 최소한 여러함수로 나눠야 마땅하다.
- 10-5를 10-6~8까지 작은 함수와 클래스로 나눈 후 함수와 클래스와 변수에 좀 더 의미 있는 이름을 부여했다.
- 또한 기존 프로그램을 세 가지 책임으로 나누었다.

```java
// 10-6
// PrimePrinter.java (Refactored code)
package literatePrimes;

public class PrimePrinter {
    public static void main(String[] args) {
        final int NUMBER_OF_PRIMES = 1000;
        int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);
        
        final int ROWS_PER_PAGE = 50;
        final int COLUMNS_PER_PAGE = 4;
        RowColumnPagePrinter tablePrinter = new RowColumnPagePrinter(ROWS_PER_PAGE, COLUMNS_PER_PAGE, 
                                                                     "The First " + NUMBER_OF_PRIMES + " Prime Numbers");
        tablePrinter.print(primes);
    }
}
```

- PrimePrinter 클래스는 main 함수 하나만 포함하며 실행 환경을 책임진다.

```java
// 10-7
// RowColumnPagePrinter.java
package literatePrimes;

import java.io.PrintStream;

public class RowColumnPagePrinter { 
    private int rowsPerPage;
    private int columnsPerPage; 
    private int numbersPerPage; 
    private String pageHeader; 
    private PrintStream printStream;

    public RowColumnPagePrinter(int rowsPerPage, int columnsPerPage, String pageHeader) { 
        this.rowsPerPage = rowsPerPage;
        this.columnsPerPage = columnsPerPage; 
        this.pageHeader = pageHeader;
        numbersPerPage = rowsPerPage * columnsPerPage; 
        printStream = System.out;
    }

    public void print(int data[]) { 
        int pageNumber = 1;
        for (int firstIndexOnPage = 0 ; 
            firstIndexOnPage < data.length ; 
            firstIndexOnPage += numbersPerPage) { 
            int lastIndexOnPage =  Math.min(firstIndexOnPage + numbersPerPage - 1, data.length - 1);
            printPageHeader(pageHeader, pageNumber); 
            printPage(firstIndexOnPage, lastIndexOnPage, data); 
            printStream.println("\f");
            pageNumber++;
        } 
    }

    private void printPage(int firstIndexOnPage, int lastIndexOnPage, int[] data) { 
        int firstIndexOfLastRowOnPage =
        firstIndexOnPage + rowsPerPage - 1;
        for (int firstIndexInRow = firstIndexOnPage ; 
            firstIndexInRow <= firstIndexOfLastRowOnPage ;
            firstIndexInRow++) { 
            printRow(firstIndexInRow, lastIndexOnPage, data); 
            printStream.println("");
        } 
    }

    private void printRow(int firstIndexInRow, int lastIndexOnPage, int[] data) {
        for (int column = 0; column < columnsPerPage; column++) {
            int index = firstIndexInRow + column * rowsPerPage; 
            if (index <= lastIndexOnPage)
                printStream.format("%10d", data[index]); 
        }
    }

    private void printPageHeader(String pageHeader, int pageNumber) {
        printStream.println(pageHeader + " --- Page " + pageNumber);
        printStream.println(""); 
    }

    public void setOutput(PrintStream printStream) { 
        this.printStream = printStream;
    } 
}
```

- 위 클래스는 숫자 목록을 주어진 행과 열에 맞춰 페이지에 출력하는 방법을 안다.
- 출력하는 모양새를 바꾸려면 RowColumnpagePrinter 클래스를 고쳐준다.

```java
// 10-8
// PrimeGenerator.java
package literatePrimes;

import java.util.ArrayList;

public class PrimeGenerator {
    private static int[] primes;
    private static ArrayList<Integer> multiplesOfPrimeFactors;

    protected static int[] generate(int n) {
        primes = new int[n];
        multiplesOfPrimeFactors = new ArrayList<Integer>(); 
        set2AsFirstPrime(); 
        checkOddNumbersForSubsequentPrimes();
        return primes; 
    }

    private static void set2AsFirstPrime() { 
        primes[0] = 2; 
        multiplesOfPrimeFactors.add(2);
    }

    private static void checkOddNumbersForSubsequentPrimes() { 
        int primeIndex = 1;
        for (int candidate = 3 ; primeIndex < primes.length ; candidate += 2) { 
            if (isPrime(candidate))
                primes[primeIndex++] = candidate; 
        }
    }

    private static boolean isPrime(int candidate) {
        if (isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
            multiplesOfPrimeFactors.add(candidate);
            return false; 
        }
        return isNotMultipleOfAnyPreviousPrimeFactor(candidate); 
    }

    private static boolean isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
        int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
        int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor; 
        return candidate == leastRelevantMultiple;
    }

    private static boolean isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
        for (int n = 1; n < multiplesOfPrimeFactors.size(); n++) {
            if (isMultipleOfNthPrimeFactor(candidate, n)) 
                return false;
        }
        return true; 
    }

    private static boolean isMultipleOfNthPrimeFactor(int candidate, int n) {
        return candidate == smallestOddNthMultipleNotLessThanCandidate(candidate, n);
    }

    private static int smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
        int multiple = multiplesOfPrimeFactors.get(n); 
        while (multiple < candidate)
            multiple += 2 * primes[n]; 
        multiplesOfPrimeFactors.set(n, multiple); 
        return multiple;
    } 
}
```

- 위 클래스는 소수 목록을 생성하는 방법을 안다.
- 객체로 인스턴스화하는 클래스가 아니다. 단순히 변수를 선언하고 감추려고 사용하는 유용한 공간일 뿐이다.
- 소수를 계산하는 알고리즘이 바뀐다면 PrimeGenerator 클래스를 고쳐준다.

</br>

- 한 쪽을 조금 넘겼던 프로그램이 거의 세 쪽으로 늘어났다. 길이가 늘어난 이유는 여러가지다.
  1. 좀 더 길고 서술적인 변수 이름을 사용한다.
  2. 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용한다.
  3. 가독성을 높이고자 공백을 추가하고 형식을 맞추었다.

</br>

## 변경하기 쉬운 클래스

- 대다수 시스템은 지속적인 변경이 가해진다. 그리고 무언가 수정할 때마다 시스템이 의도대로 동작하지 않을 위험이 따른다.

- 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.

```java
// 10-9
// 변경이 필요해 손대야 하는 클래스
public class Sql {
    public Sql(String table, Column[] columns)
    public String create()
    public String insert(Object[] fields)
    public String selectAll()
    public String findByKey(String keyColumn, String keyValue)
    public String select(Column column, String pattern)
    public String select(Criteria criteria)
    public String preparedInsert()
    private String columnList(Column[] columns)
    private String valuesList(Object[] fields, final Column[] columns) private String selectWithCriteria(String criteria)
    private String placeholderList(Column[] columns)
}
```

- 주어진 메타 자료로 적절한 SQL 문자열을 만드는 Sql 클래스이다.
- 새로운 SQL 문을 지우런하거나 기존 SQL 문 하나를 수정할 때 반드시 Sql 클래스를 수정해야 한다.
- **문제는 코드에 손을 대면 위험이 생긴다.**
- 변경할 이유가 두 가지 이므로 Sql 클래스는 SRP를 위반한다.
  - 어떤 변경이든 클래스에 손대면 다른 코드를 망가뜨릴 잠정적인 위험이 존재한다. 그래서 테스트도 완전히 다시해야 한다.
  - 또한 기존 SQL문 하나를 수정할 때도 반드시 Sql 클래스에 손을 대야한다.
    - select 문에 내장된 select 문을 지원하려면 Sql 클래스를 고쳐야한다.
- 저자의 경험에 의하면 클래스 일부에서만 사용되는 비공개 메서드는 코드를 개선할 잠재적 여지를 시사한다. 하지만 실제로 개선에 뛰어드는 계기는 시스템이 변해서라야 한다.

```java
// 10-10
// 닫힌 클래스 집합
abstract public class Sql {
    public Sql(String table, Column[] columns) 
    abstract public String generate();
}
public class CreateSql extends Sql {
    public CreateSql(String table, Column[] columns) 
    @Override public String generate()
}

public class SelectSql extends Sql {
    public SelectSql(String table, Column[] columns) 
    @Override public String generate()
}

public class InsertSql extends Sql {
    public InsertSql(String table, Column[] columns, Object[] fields) 
    @Override public String generate()
    private String valuesList(Object[] fields, final Column[] columns)
}

public class SelectWithCriteriaSql extends Sql { 
    public SelectWithCriteriaSql(
    String table, Column[] columns, Criteria criteria) 
    @Override public String generate()
}

public class SelectWithMatchSql extends Sql { 
    public SelectWithMatchSql(String table, Column[] columns, Column column, String pattern) 
    @Override public String generate()
}

public class FindByKeySql extends Sql public FindByKeySql(
    String table, Column[] columns, String keyColumn, String keyValue) 
    @Override public String generate()
}

public class PreparedInsertSql extends Sql {
    public PreparedInsertSql(String table, Column[] columns) 
    @Override public String generate() {
    private String placeholderList(Column[] columns)
}

public class Where {
    public Where(String criteria) public String generate()
}

public class ColumnList {
    public ColumnList(Column[] columns) public String generate()
}
```

- 공개 인터페이스를 각각 Sql 클래스에서 파생하는 클래스로 만들었다.

- valueList와 같은 비공개 메서드는 해당하는 파생 클래스로 옮겼다.
- 모든 파생 클래스가 공통으로 사용하는 비공개 메서드는 Where와 ColumnList라는 두 유틸리티 클래스에 넣었다.
- 이제 update 문을 추가할 때 기존 클래스를 변경할 필요가 없다.
  - Sql 클래스에서 새 클래스 UpdateSql을 상속받아 거기에 넣으면 된다.

- 위 클래스는 SRP 뿐만 아니라 확장에 개방적이고 수정에 폐쇄적인 OCP도 지원한다.
  - 파생 클래스를 생성하는 방식으로 새 기능에 개방적인 동시에 다른 클래스를 닫아 놓는 방식으로 수정에 폐쇄적이다.

### 변경으로부터 격리

> 요구사항은 변하기 마련이고 코드도 변하기 마련이다.

- 우리는 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리한다.
- 상세한 구현에 의존하는 코드는 테스트가 어렵다.

예를 들어, Portfolio 클래스를 만든다고 가정하자. 그런데 Portfolio 클래스는 외부 TokyoStockExchange API를 사용해 포트폴리오 값을 계산하다. 따라서 우리 테스트 코드는 시세 변화에 영향을 받는다.

</br>

```java
public interface StockExchange {
    Money currentPrice(String symbol);
}
```

Portfolio 클래스에서 TokyoStockExchange API를 직접 호출하는 대신 StockExchange라는 인터페이스를 생성한 후 메서드 하나를 선언한다.

```java
public Portfolio {
    private StockExchange exchange;
    public Portfolio(StockExchange exchange) {
        this.exchange = exchange;
    }
    // ...
}
```

이제 TokyoStockExchange 클래스를 흉내내는 테스트용 클래스를 만들 수 있다.

```java
public class PortfolioTest {
    private FixedStockExchangeStub exchange;
    private Portfolio portfolio;

    @Before
    protected void setUp() throws Exception {
        exchange = new FixedStockExchangeStub(); 
        exchange.fix("MSFT", 100);
        portfolio = new Portfolio(exchange);
    }

    @Test
    public void GivenFiveMSFTTotalShouldBe500() throws Exception {
        portfolio.add(5, "MSFT");
        Assert.assertEquals(500, portfolio.value()); 
    }
}
```

테스트용 클래스는 StockExchange 인터페이스를 구현하며 고정된 주가를 반환한다.

StockExchange 인터페이스는 주식 기호를 받아 현재 주식 가격을 반환한다는 추상적인 개념을 표현한다.

이와 같은 추상화로 실제로 주가를 얻어오는 출처나 얻어오는 방식 등과 같은 구체적인 사실을 모두 숨긴다.