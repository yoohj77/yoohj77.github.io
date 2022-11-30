---
title : 코딩테스트연습
date : 2022-08-05 00:00:00 -09:00
categories : [코딩테스트연습, 숫자 문자열과 영단어]
tags : [코딩테스트연습, 숫자 문자열과 영단어] #소문자만 가능
---
> 1478 → "one4seveneight"
234567 → "23four5six7"
10203 → "1zerotwozero3"

이렇게 숫자의 일부 자릿수가 영단어로 바뀌어졌거나, 혹은 바뀌지 않고 그대로인 문자열 s가 매개변수로 주어집니다. s가 의미하는 원래 숫자를 return 하도록 solution 함수를 완성해주세요.

```
function solution(s) {
    var words = ['one' ,'two', 'three' ,'four', 'five', 'six', 'seven', 'eight', 'nine' ];


    var answer = 0;

    for (var i =0 ; i < words.length; i++) {
        s = s.replace(words[i], i+1);
    };
    answer = Number(s);
    
    return answer;
}

==> 테스트 케이스 통과하지 못함 60점짜리..
```
```
function solution(s) {
    let numbers = ["zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"];
    var answer = s;

    for(let i=0; i< numbers.length; i++) {
        let arr = answer.split(numbers[i]);
        answer = arr.join(i);
    }

    return Number(answer);
}
```
==> 통과된 예시를 확인해보면 이렇다.