# 원천피플 스터디
## 개요
* 기간: 1학기 내내 (3개월, 3월 23일 ~ 7월 4일)
* 방식
	* 오프라인 주 1회, 평일(주로 목요일) 7시 30분
	* 오프라인 안될 시 온라인으로 조정
	* 격주로 2인씩 스프링/코테 돌아가면서 진행
		* 형주/상수
		* 유빈/대민

## 프로젝트 클론
1. 본인의 개인 리포지토리로 포크하기
2. 포크한 리포지토리 클론하기
   ```bash
	 git clone https://github.com/{본인의 GitHub 계정명}/home
	 ```
3. 클론한 프로젝트 폴더 접근 후 업스트림 리포지토리 추가
   ```bash
	 git remote add upstream https://github.com/woncheon-people/home.git
	 ```
4. `git remote -v`로 origin과 upstream 리포지토리 확인
## 스터디 내용 추가하는 방법 (Pull Request)
1. 내용 추가 전 새로운 브랜치 따기
	* 현재 브랜치 확인하고 `main` 브랜치로 전환
		* `git pull upstream main`으로 최신 소스 가져오기
			* 오류가 생긴 경우 `git reset --hard upstream/main`으로 헤드 정렬
	* `git branch {새 브랜치명}`으로 브랜치 먼저 따기
	* `git switch {새 브랜치명}`으로 브랜치 전환하기
2. 작업 완료 후 해당 브랜치 origin에 푸시
	* 작업 내용 커밋하기
	 ```bash
		git add -A
		git commit -m "커밋 코멘트"
	 ```
   * 커밋한 작업 푸시하기
   ```bash
		git push -u origin {브랜치명}
	 ```
3. GitHub의 업스트림 리포지토리에 Pull Request 요청하기

## 스프링 스터디
### 개요
인프런 [김영한의 스프링 완전 정복](https://www.inflearn.com/roadmaps/373) 코스를 따라 스프링으로 웹 어플리케이션을 개발하는 기법을 익힙니다.
### 로드맵
* ~~[스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8) (완료)~~
* ~~[모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC) (완료)~~
* [**스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술**](_https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1_) (**진행중**)
* [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술](_https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2_)
* [스프링 DB 1편 - 데이터 접근 핵심 원리](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1)
* [스프링 DB 2편 - 데이터 접근 활용 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2)
* [스프링 핵심 원리 - 고급편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8)
* [스프링 부트 - 핵심 원리와 활용](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9)

### 진행 상황
* 2023년 4월 12일 기준
* **스프링 MVC 1편 - 챕터 5** 까지 진행
## 코딩 테스트 대비
### 개요
백준 [단계별로 풀어보기](https://www.acmicpc.net/step)의 **시간 복잡도** 단계부터 순차적으로 해결합니다.
### 양식
[양식 보기](/coding-test/Template.md)

### 매주 과제
* 매 주 문제 두개로 시작해서 풀어봅시다.
* 문제와 관련된 알고리즘 혹은 풀이 기법에 대한 설명을 같이 조사해옵니다.
* 언어는 아래 선택한 언어를 사용. 복수선택 가능
	| 언어 		| 선택한 사람					|
	|--------|--------------------|
	| Python | 형주, 상수, 대민 		|
	| C/C++  | 상수  							 |
	| Java 	 | 유빈 							 |
 
### 진행 상황
* 2023년 4월 12일 기준
* [**13단계 - 정렬**](https://www.acmicpc.net/step/9) 까지 진행
## 기술 면접 대비
### 개요
기술 면접을 대비하여 필요한 CS 전공지식을 스터디합니다.

### 진행 방식
- 별도의 스터디는 진행하지 않습니다.
- 평소에 학교 등에서 수강한 전공 지식을 자유롭게 공유합니다.
## 프로젝트
* 프로젝트는 스터디 기간이 종료되면 재개 예정입니다.


