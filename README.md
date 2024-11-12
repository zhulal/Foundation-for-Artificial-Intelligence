# Foundation-for-Artificial-Intelligence

```markdown
# Portfolio Assignment - Data Science and Analysis

## No 2: Paper Money and Coins Calculation

### Problem Description
The task requires creating a Python program to simulate a bank's process of liquidating customer savings into paper money and coins. The bank is required to issue the largest denominations first, and it must handle requests up to a limit of 1 billion Rupiah. If a customer's savings cannot be entirely cashed out due to an insufficient combination of denominations, the program will inform the user.

The denominations considered are:
- **Paper Money**: Rp 100,000, Rp 50,000, Rp 20,000, Rp 10,000, Rp 5,000, Rp 2,000
- **Coins**: Rp 1,000, Rp 500, Rp 200, Rp 100 (Note: Rp 1,000 is considered a coin in this scenario)

The Python program accepts an integer input (customer's savings) between 0 and 1 billion Rupiah and outputs the breakdown of how many notes and coins are needed.

### Code Implementation
```python
def cash_out(num):
    # Daftar denominasi dalam urutan menurun
    denominations = [100000, 50000, 20000, 10000, 5000, 2000, 1000, 500, 200, 100]
    denomination_count = {denom: 0 for denom in denominations}
    total_paper_money = 0
    total_coins = 0

    # Iterasi setiap denominasi dan hitung jumlah uang kertas/koin
    for denom in denominations:
        if num >= denom:
            count = num // denom
            denomination_count[denom] = count
            num %= denom

    # Hitung total uang kertas dan koin
    for denom in denominations:
        if denom >= 1000:
            total_paper_money += denomination_count[denom]
        else:
            total_coins += denomination_count[denom]

    # Output
    for denom in denominations:
        print(f">> Amount of currency Rp {denom:,}: {denomination_count[denom]}")

    print(f"\n>> Total Paper Money: {total_paper_money}")
    print(f">> Total Coins: {total_coins}")
    print(f">> Cannot be cashed out: {num}")

try:
    num = int(input("Input\n>> "))
    batas_maksimal = 1000000000
    if num < 0:
        print("Please input the correct number")
    elif num > batas_maksimal:
        print(f">> Maximum Saving is {batas_maksimal}")
    else:
        cash_out(num)
except ValueError:
    print("Please input a valid number")
```

### Sample Output
```
Input
>> 1245123
You entered: 1245123

>> Amount of currency Rp 100,000: 12
>> Amount of currency Rp 50,000: 0
>> Amount of currency Rp 20,000: 2
>> Amount of currency Rp 10,000: 0
>> Amount of currency Rp 5,000: 1
>> Amount of currency Rp 2,000: 0
>> Amount of currency Rp 1,000: 0
>> Amount of currency Rp 500: 0
>> Amount of currency Rp 200: 0
>> Amount of currency Rp 100: 1

>> Total Paper Money: 15
>> Total Coins: 1
>> Cannot be cashed out: 23
```

## No 3: Pandas Data Analysis

### Problem Description
The task involves performing several data analysis operations on a provided HR dataset using Python and Pandas. The following operations are included:

1. **Calculate the minimum, median, maximum, and average salary by marital status and gender**.
2. **Find the top 5 reasons for employee termination**.
3. **Identify the highest number of employees with an "Exceeds" performance score by recruitment source**.
4. **Count how many managers there are in each department**.
5. **Calculate the termination ratio by gender**.

### Code Implementation
```python
import pandas as pd
import warnings
warnings.filterwarnings("ignore")

# Load Data
data = pd.read_csv('https://raw.githubusercontent.com/Rietaros/kampus_merdeka/main/HRDataset_v14.csv')

# 1. Minimum, median, max, and average salary by Married Description and Gender
result = data.groupby(['MaritalDesc', 'Sex']).agg(
    min_salary=('Salary', 'min'),
    median_salary=('Salary', 'median'),
    max_salary=('Salary', 'max'),
    avg_salary=('Salary', 'mean')
)
print(result)

# 2. Top 5 reasons for termination
top5Term = data['TermReason'].value_counts().head(5)
term_reason_df = top5Term.reset_index()
term_reason_df.columns = ['TermReason', 'total_termination']
print(term_reason_df)

# 3. Highest number of employees by Recruitment Source with 'Exceeds' performance score
exceeds_df = data[data['PerformanceScore'] == 'Exceeds']
exceeds_df = exceeds_df.groupby('RecruitmentSource').size().reset_index(name='total_recuit').sort_values(by='total_recuit', ascending=False)
print(exceeds_df)

# 4. Number of managers in each department
manager_counts = data.groupby('Department')['ManagerName'].nunique().reset_index(name='ManagerCount')
print(manager_counts)

# 5. Termination Ratio by Gender
termination_ratio = data.groupby('Sex')['Termd'].value_counts(normalize=True).sort_index().rename('Termd')
print(termination_ratio)
```

### Expected Output (Sample Results)
1. **Salary Statistics by Marital Status and Gender**:
```
                 min_salary  median_salary  max_salary   avg_salary
MaritalDesc Sex                                                     
Divorced    F         45069        59393.0       68182  57345.500000
            M         45115        63738.5      148999  72520.857143
... 
```

2. **Top 5 Termination Reasons**:
```
       TermReason  total_termination
N/A-StillEmployed                207
Another position                 20
unhappy                          14
more money                       11
career change                     9
```

3. **Top Recruitment Sources for 'Exceeds' Performance**:
```
RecruitmentSource  total_recuit
            Indeed            12
          LinkedIn             9
... 
```

4. **Number of Managers per Department**:
```
          Department  ManagerCount
       Admin Offices             3
       Executive Office           1
       IT/IS                     6
... 
```

5. **Termination Ratio by Gender**:
```
Sex  Termd
F    0        0.659091
     1        0.340909
M    0        0.674074
     1        0.325926
```

### Visualizations (EDA)

1. **Termination by Gender (Bar Chart)**:
```python
sns.barplot(x='Sex', y='total_termination', hue='Sex', data=term, palette='cool')
plt.show()
```

2. **Salary vs Engagement Survey (Scatter Plot)**:
```python
sns.scatterplot(x='Salary', y='EngagementSurvey', hue='Termd', palette={0: 'blue', 1: 'red'}, data=data)
plt.show()
```

3. **Termination Counts by Department (Bar Chart)**:
```python
sns.barplot(x='Termd', y='Department', data=termination_counts, hue='Department', palette='hls')
plt.show()
```

4. **Termination of Employees by Position (Pie Chart)**:
```python
termination_counts.plot.pie(y='Termd', labels=termination_counts['Position'], legend=False, autopct='%1.1f%%')
plt.show()
```

5. **Salary by Marital Status (Box Plot)**:
```python
sns.boxplot(data=data, x='Salary', y='MaritalDesc', hue='Termd', orient="h")
plt.show()
```

### Conclusion
This analysis demonstrates how various demographic and organizational factors affect employee salaries, termination rates, and performance. The insights from these visualizations and calculations can inform HR decision-making and help optimize employee management strategies.
```
