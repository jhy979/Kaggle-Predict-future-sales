Team go!
kaggle Predict Future Sales

실행 방법

1. Kaggle에서 Predict Future Sales대회를 찾습니다.

2. Data란에서 sample_submission.csv를 제외한 나머지 5개 데이터 파일을 다운로드 받습니다.

3. 다운로드 받은 Data들을 자신의 구글 드라이브에 넣습니다.

4. Team_go!_predict future sales.ipynb 파일을 코랩으로 연결시켜 실행시킵니다.

5. 3번째 코드에서 Data들을 넣은 경로를 ROOT에 지정해줍니다. (예시 :  ROOT = '/content/gdrive/MyDrive/Colab Notebooks/') 

6. 코드를 실행시킵니다.
- 실행시키면  drive.mount('/content/gdrive/') 를 통해 구글 드라이브에 코랩을 연결하게 되고, ROOT경로에 들어있는 Data들을 pd.read_csv를 통해 읽어들입니다.

7. 학습이 종료되면 submission.csv파일이 생성됩니다.

8. 이 파일을 Kaggle에 제출합니다.