## [의약품 안전나라 이슈내역 추출] - https://nedrug.mfds.go.kr/  ##
> 의약품 안전나라 사이트에 접속하여 취합한 자료(행정처분) 중 이슈 내역을 정리하여 담당자에게 공유      
> REFramework로 구현 (QueueItem이 아닌 **DataRow** 이용)
>  - TransactionData :: 기존=DataTable, 변경=**DataTable**
>  - TransactionItem :: 기존=QueueItem, 변경=**DataRow**   


#### [작업순서] ####
> _자동화설계_프로세스맵구상.xlsx 파일 참고
> 검색결과 스크래핑 시 [순번]도 함께 스크래핑 해서 조회결과 없을 경우 Data가 없는것이 아닌 1건 있는것으로 나옴   
> - 조회결과 없을경우 스크래핑 데이터=Nothing으로 처리함.   
1. [의약품 안전나라] 사이트 접속 > [고시/공고/알림] 클릭 > [행정처분정보] 클릭   
2. 검색 조건 세팅 - 품목구분 = 의약품등, 처분일자기간 = 전월 1일 ~ 당월 마지막날까지 > [검색] 클릭   
3. 검색 결과 데이터스크래핑 - [순번], [업체명], [URL] => TransactionData   
4. NavigateTo 행정처분정보 페이지로 이동. 필요한 항목 가져오기 [업체명], [처분일자], [위반내역], [처분사항] => "데이터테이블A"   
5. "데이터테이블A" 에서 이슈사항 필터링(거짓, 허위 등등)해서 "데이터테이블B"에 저장
6. "데이터테이블B" 에서 처분일자 필터링(당월이슈사항)해서 "데이터테이블C"에 저장
7. 엑셀 각 시트에 해당 내용 쓰기
8. 결과파일 첨부하여 메일발송. 이슈사항 테이블도 추가(html)


#### [Config.xlsx 참고] ####
* strExcelMacroFile - 엑셀에 적용할 레이아웃 VBA
* strWebUrl - 의약품 안전나라 URL
* strTemplateFile - 엑셀 결과파일 템플릿
* strResultFolder - 결과파일 저장 폴더
* strResultFile - 결과파일명
* strSearchItem - 행정처분정보>품목구분 (전체, 의약품등, 화장품) - 추후 품목 변경해서 추출하고자 할 경우를 대비
* strFilterColumn - 필터링 적용할 컬럼
* strFilterKeywords - 필터링 키워드들 (변경될 것을 대비해 config에서 관리)
* strToMailAccount - 결과메일 수신자
* strCCMailAccount - CC 메일계정
* strOutlookAccount - 아웃룩 메일 계정
* strMailSubject - 메일 제목 [보내는사람이름] 00월 의약품 안전나라 행정처분사항 공유
* strMailBody - 메일 내용(이슈사항 테이블 포함) - html

  
#### [추가된 파일(Invoke) 정보] ####
* Initalization > Bizwork\CreateFolderFile.xaml - 폴더, 파일 처리. 폴더 없으면 생성, 파일 있으면 삭제
* EndProcess > Bizwork\WriteExcelFile.xaml - 엑셀 파일 쓰기
* EndProcess > Bizwork\GetHTMLTableTagFromDataTable.xaml - DataTable 데이터를 표 형태로(Table tag)로 변환
* EndProcess > Bizwork\SendOutlookMail.xaml - 결과메일발송(아웃룩)


#### [How It Works] ####

1. **INITIALIZE PROCESS**
   + Config.xlsx 설정
   + 브라우저(Chrome, Excel) 강제종료
   + 폴더, 파일 관리(폴더 있으면 삭제 후 생성, 파일 있으면 삭제)
   + [의약품 안전나라] 사이트 접속 > [고시/공고/알림] 클릭 > [행정처분정보] 클릭
   + 검색 조건 세팅 - 품목구분 = 의약품등, 처분일자기간 = 전월 1일 ~ 당월 마지막날까지 > [검색] 클릭
   + 검색 결과 데이터스크래핑 - [순번], [업체명], [URL] => TransactionData
   +  - 품목구분 select item에 config에 설정한 항목 없을 경우 => TransactionData=nothing
      - 검색 결과 없을 경우 => TransactionData=nothing

2. **GET TRANSACTION DATA**
   + Get transaction item 비활성
   + 처리할 TransactionItem 체크

4. **PROCESS TRANSACTION**
   + NavigateTo 행정처분정보 페이지로 이동
   + 필요한 항목 가져오기 [업체명], [처분일자], [위반내역], [처분사항] => "데이터테이블A"

4. **END PROCESS**
   + 브라우저 닫기
   + TransactionData isNot Nothing 일 경우 아래 진행
   +  - "데이터테이블A" 에서 이슈사항 필터링해서 "데이터테이블B"에 저장
      - "데이터테이블B" 에서 처분일자 필터링해서 "데이터테이블C"에 저장
      - 엑셀 각 시트에 해당 내용 쓰기
      - 결과파일 첨부하여 메일발송

* * *
![get_IssueDetails_From_nedrug mfds go kr_guide](https://github.com/pnmGithub/get_IssueDetails_From_nedrug.mfds.go.kr.RPA-uipath/assets/149296871/c7f24481-3431-40ad-9567-477f1d5f5eda)

* * *

### REFrameWork Template ###
**Robotic Enterprise Framework**
