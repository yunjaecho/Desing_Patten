디자인 패턴 (Visitor Patten) 
======================

# Visitor Patten(방문 패턴) 무엇인가?

###### 일반적인경우 OOP에서는 객체가 스스로 행위에 대한 내용을 포함하도록 하지만,  
###### 객체에 대한 행위의 내용을 외부 클래스로 빼서 객체의 행위를 위임하기도 한다.   
###### 이런 타입의 패턴으로 전략패턴, 커맨드 패턴, 비지터 패턴등이 있다. 셋 모두 객체의 행위를 바깥으로 위임하는 것이지만,   
###### 전략패턴이 하나의 객체에 대해 여러 동작을 하게 하거나(1:N), 커맨드 패턴이 하나의 객체에 대하 하나의 동작(+보조동작)에 대한 설계방식(1:1)인 반면에,   
###### 방문자 패턴은 여러 객체들에 대해 객체의 동작들을 지정하는 방식(N:N) 이다.   
###### N대 N이다 보니 구현에 조금 복잡해진다.  
###### 두개의 N에 대해 처리해야 하다보니 두개의 인터페이스(비지터 인터페이스와 엘리먼트 인터페이스)가 필요하다. 
###### 
###### 
###### 많이 사용되는 부분은 컴파일러부분이다.   
###### 소스코드는 여러 primitive type,파라미터,함수이름,키워드등으로 이루어져 있는데, 
###### 각 엘리먼트는 컴파일시에 부분부분 바이트 코드 혹은 기계어, 혹은 힌트,에러코드등으로 변환되어야한다.   
###### 이럴때 다양한 엘리먼트에 대한 사전 처리 방법이 적용되어있는 비지터 패턴을 적용하기 좋다.    
###### 
###### 
###### 일상생활의 예로 집안도우미 서비스를 생각해볼수 있다.   
###### 요즘 맞벌이 집에서는 가끔 가정도우미 서비스를 이용한다.   
###### 집은 일반적으로 청소를 해줘야하고, 음식을 차리고 설겆이를 해야 하고, 아이가 있는 경우 아이돌보기도 해줘야 한다.   
###### 집에서 모두 직접할수 있지만 요즘은 가끔 이런 서비스를 외부에 맞기기도 한다.  
###### 
###### 
###### A라는 집안도우미 서비스업체가 있고,B라는 집안도우미 서비스 업체가 있다. 두 회사는 모두 요리서비스, 청소서비스, 육아서비스를 제공한다.    
###### 선택에 따라 A업체의 서비스를 받을수도 있고, B업체의 서비스도 받을 수 있다.   
###### 이경우 visitor pattern에서는 아래와 같이 클래스를 디자인 할수 있다  
###### [출처] 방문자 패턴(Visitor Pattern) - 자바 디자인 패턴과 JDK예제|작성자 IDEO  



# 구조화 데이터의 취급 Visitor 패턴
## JAVA(Visitor 패턴) 경우
#### 개체지향 언어에서 구조를 갖는 데이터를 추적하는 처리를 허려면 디자인 패턴에서 말하는 Vistor Patten을 자주 사용하게 된다.
#### 실제로 XMLSAX 라이브러리나 파서 생성기(parser generator) 등에서는 Visitor 패턴의 인터페이스를 생성하여 프로그램머에게 제공한다.
#### 여기서는 숫자의 덧셈과 제곱만을 갖는 식의 구문 트리를 검색하여 수식을 평가하거나 수식을 그래도 문자열로 하거나 하는 작업을 생각해 보자.
#### 수식은 다음과 같은 BNF로 표현한다고 치자.

### 식 :: 식 '+' '(' 식 ')' ^2 | 숫자

![classdialgram](https://user-images.githubusercontent.com/12265888/45093133-74cfa500-b152-11e8-870c-d33d2faefc87.png)

## JAVA Source
interface Visitor<N, R> {
    //덧셀 식을 위한 메소드
    R plus(Expr<N> e1, Expr<N> e2);
    // 제곱 식을 우ㅏ한 메소드
    R square(Expr<N> e);
    // 숫자를 위한 메소드
    R number(N e);
}

// 식의 인터페이스
interface Expr<N> {
    <R> R accept(Visitor<N, R> v);
}

// 덧셈 식
class Plus<N> implements Expr<N> {
    Expr<N> e1;
    Expr<N> e2;

    Plus(Expr<N> e1, Expr<N> e2) {
        this.e1 = e1;
        this.e2 = e2;
    }

    public <R> R accept(Visitor<N, R> v) {
        return v.plus(e1, e2);
    }
}

// 제곱 식
class Square<N> implements Expr<N> {
    Expr<N> e1;

    Square(Expr<N> e1) {
        this.e1 = e1;
    }

    public <R> R accept(Visitor<N, R> v) {
        return v.square(e1);
    }
}

// 숫자 식
class Number<N> implements Expr<N> {
    N n;

    Number(N n) {
        this.n = n;
    }

    public <R> R accept(Visitor<N, R> v) {
        return v.number(n);
    }
}

// 식의 평가를 실시하는 Visitor
class Eval implements Visitor<Integer, Integer> {

    public Integer plus(Expr<Integer> e1, Expr<Integer> e2) {
        return e1.accept(this) + e2.accept(this);
    }

    public Integer square(Expr<Integer> e) {
        Integer x = e.accept(this);
        return x * x;
    }

    public Integer number(Integer n) {
        return n;
    }
}

class Show implements Visitor<Integer, String> {
    public String plus(Expr<Integer> e1, Expr<Integer> e2) {  
        return e1.accept(this) + " + " + e2.accept(this);  
    }

    public String square(Expr<Integer> e) {
        return "(" + e.accept(this) + ")^2" ;
    }

    public String number(Integer n) {
        return n + "";
    }
}

public class TestVisitor {  
    public static void main(String[] args) {  
        // e - 1 +(2 + 3)^2  
        Expr e = new Plus(new Number(1),  
                          new Square(new Plus(new Number(2), new Number(3))));  

        System.out.println(e.accept(new Show()));  
        System.out.println(e.accept(new Eval()));  
    }  
} 
