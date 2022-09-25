# read_data: 파일의 각 행으로부터 2개의 정수를 읽어서 리스트에 저장 
def read_data(filename):
    data = [] # 빈 리스트 준비
    with open(filename, 'r') as fi:
        for line in fi.readlines():
            if line.startswith('#'):
                continue
            # 두 정수를 리스트로 묶어서 저장
            values = [int(text) for text in line.split(',')]
            data.append(values)
    return data


# add_weighted_average: 각 행에 대한 가중 평균 계산
# 가중평균: total = weight[0] * mid + weight[1] * final
def add_weighted_average(data, weight):
    for row in data:
        mid = row[0]
        final = row[1]
        total = weight[0] * mid + weight[1] * final
        row.append(total) # 칼럼 항목 추가


def mean_var(data):
    n = len(data)
    if n > 0:
        mean = sum(data) / n
        sum2 = sum([e**2 for e in data])
        var = sum2 / n - mean**2
        return mean, var
    return None, None


# analyze_data: 각 열에 대한 평균, 분산, 중앙값, 최솟값, 최댓값 구하기
def analyze_data(data):
    mean, var = mean_var(data)
    median = sorted(data)[len(data) // 2]
    return mean, var, median, min(data), max(data)


# 해당 모듈이 임포트 된 경우가 아니라 인터프리터에서 직접 실행된 경우에만,
# if문 이하의 코드를 돌려라!
if __name__ == '__main__':
    data = read_data('../data/class_score_en.csv')
    for row in data:
        # 중간, 기말
        if data and len(data[0]) == 2: # Check 'data' is valid
            add_weighted_average(data, [40/125, 60/100])
            # 중간, 기말, 가중 평균
            if len(data[0]) == 3:      # Check 'data' is valid
                print('### Individual Score')
                print()
                print('| Midterm | Final | Total |')
                print('| ------- | ----- | ----- |')
                for row in data:
                    print(f'| {row[0]} | {row[1]} | {row[2]:.3f} |')
                print()

                print('### Examination Analysis')
                col_n = len(data[0])
                col_name = ['Midterm', 'Final', 'Total']
                colwise_data = [ [row[c] for row in data] for c in range(col_n) ]

                # 중간, 기말, 가중평균에 대한 평균, 분산, 중앙값, 최소/최대 값 구하기
                for c, score in enumerate(colwise_data):
                    mean, var, median, min_, max_ = analyze_data(score)
                    print(f'* {col_name[c]}')
                    print(f'  * Mean: **{mean:.3f}**')
                    print(f'  * Variance: {var:.3f}')
                    print(f'  * Median: **{median:.3f}**')
                    print(f'  * Min/Max: ({min_:.3f}, {max_:.3f})')
