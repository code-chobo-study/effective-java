## 아이템 12. toString을 항상 재정의 하라
- PhoneNumber 객체의 예제 
    ```java 
        public final class PhoneNumber {
            private final short areaCode, prefix, lineNum;
        
            public PhoneNumber(int areaCode, int prefix, int lineNum) {
                this.areaCode = rangeCheck(areaCode, 999, "지역코드");
                this.prefix   = rangeCheck(prefix,   999, "프리픽스");
                this.lineNum  = rangeCheck(lineNum, 9999, "가입자 번호");
            }
         
        }
    
    ```
- toString 은 항상 `간결하면서 사람이 읽기 쉬운 형태의 유익한 정보`를 반환해야 한다. 
- 객체가 가진 모든 정보를 보여주는 것이 좋다.
  ```java 
     /**
     * 이 전화번호의 문자열 표현을 반환한다.
     * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
     * XXX는 지역 코드, YYY는 프리픽스, ZZZZ는 가입자 번호다.
     * 각각의 대문자는 10진수 숫자 하나를 나타낸다.
     *
     * 전화번호의 각 부분의 값이 너무 작아서 자릿수를 채울 수 없다면,
     * 앞에서부터 0으로 채워나간다. 예컨대 가입자 번호가 123이라면
     * 전화번호의 마지막 네 문자는 "0123"이 된다.
     */
    @Override public String toString() {
        return String.format("%03d-%03d-%04d",
                areaCode, prefix, lineNum);
    }
  ```
  ```
  010-123-4567
  ```
  
- 값 클래스 라면 포맷을 문서에 명시하는 것이 좋으며 해당 포맷으로 객체를 생성할 수 있는 정적 팩터리나 생성자를 제공 하는 것이 좋다. 
  ```java 
    public static PhoneNumber of(String phoneNumberString) {
       String[] split = phoneNumberString.split("-");
       PhoneNumber phoneNumber = new PhoneNumber(
                Short.parseShort(split[0]),
                Short.parseShort(split[1]),
                Short.parseShort(split[2]));
       return phoneNumber;
    }
    
    ```  
  
- Object의 toString은 `클래스이름@16진수`로 표시한 해시코드
  ```java 
  chapter02.item12.PhoneNumber@adbbd
  ```


- toString이 반환한 값에 포함된 정보를 얻어올 수 있는 API 를 제공하는 것이 좋다.  
  `어차피 toString으로 공개된 데이터이기 때문에 각각의 getter 또한 제공하라는 의미`
  ```java 
    public short getAreaCode() {
        return areaCode;
    }

    public short getPrefix() {
        return prefix;
    }

    public short getLineNum() {
        return lineNum;
    }

  ```
- 경우에 따라 AutoValue, 롬복 또는 IDE 를 사용하지 않는게 적절 할 수 있다.  
 원하는 포맷이 필요할 수 있는 경우 : `010-000-0000`
