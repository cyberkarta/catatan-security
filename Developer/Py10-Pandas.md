# Pandas Library Python
#python 
Dataset : 
https://analyse.kmi.open.ac.uk/open_dataset
https://raw.githubusercontent.com/justmarkham/pandas-videos/master/data/ufo.csv
https://www.briandunning.com/sample-data/

## Cheatsheet
https://www.shanelynn.ie/pandas-iloc-loc-select-rows-and-columns-dataframe/

Dataframe:
https://www.briandunning.com/sample-data/

Membaca CSV
```py
df = pd.read_csv(file_name)

# dari web
df = pd.read_csv(URL)
```

Mendapatkan informasi jumlah baris dan kolom
```py
df.shape()

# returns tuple ("total_baris", "total_kolom")
```
Mendapatkan value dari header
```py
df.head(0)

# mendapatkan informasi 3 baris pertama
df.head(3)

# mendapatkan value dari columns (return list)
list(df.columns)
```

Mengubah urutan dari kolom dan datanya
```py
titles = list(df.columns)

# reorder
titles[1], titles[2] = titles[2], titles[1]
titles[1], titles[2] = ["firstname", "lastname"]

# mengubah nama kolom
titles[1], titles[2] = "first_name", "last_name"
```

Insert pada kolom pertama (index start mulai dari 0)
```py
df.insert(1, column="name", value="Change")
```

Menghapus column beserta datanya
```py
df.pop("website")
```

Mendapatkan value dari row dengan menggunakan index
```py
# data row pertama
df.iloc[0] 

# data row terakhir
df.iloc[-1]

# beberapa row
df.iloc[0:5]

# menambahkan informasi column ke-0
df.iloc[:,0]
```

Mengubah nama kolom
```py
df.rename(columns={"OldName": "NewName"})
```

Menggunakan fungsi untuk mengubah nama kolom
```py
data = {'Col1_Category': ['Dog', 'Cat', 'Rabbit', 'Parrot'],
       'Col2_Color': ['brown', 'black', 'white', 'green']}
df = pd.DataFrame(data)

# print dataframe columns
print("Dataframe columns:", df.columns)

# change column names to the string after the _
df = df.rename(columns=lambda x: x.split("_")[1])

# print dataframe columns
print("Dataframe columns:", df.columns)

# output
# Dataframe columns: Index(['Col1_Category', 'Col2_Color'], dtype='object')
# Dataframe columns: Index(['Category', 'Color'], dtype='object')
```

Set index pada data
```py
# menggunakan lastanem sebagai metode indexing
df.set_index("last_name", inplace=True)

# setelah index dibuat, kita bisa mencari data last_name menggunakan label
df.loc['Beckham']
df.loc['Beckham', 'Ronaldo']

# menentukan kolom yang diambil
df.loc['Beckham', 'Ronaldo'], ['first_name', 'address', 'city']

# mengambil data pada index dan kolom dari first_name sampai dengan city
df.loc['Beckham', 'Ronaldo'], 'first_name' : 'city'
```

Tanpa set index, sebenarnya juga bisa mengambil data menurut kolom lainnya.
```py
df.loc[df['first_name'] == 'David']

# returns a series
df.loc[df['first_name'] == 'David', 'email']

# returns a dataframe
df.loc[df['first_name'] == 'David', 'email']
```

Kemungkinan penggunaan:
```py
# Select rows with first name Antonio, # and all columns between 'city' and 'email'
data.loc[data['first_name'] == 'Antonio', 'city':'email']
 
# Select rows where the email column ends with 'hotmail.com', include all columns
data.loc[data['email'].str.endswith("hotmail.com")]   
 
# Select rows with last_name equal to some values, all columns
data.loc[data['first_name'].isin(['France', 'Tyisha', 'Eric'])]   
       
# Select rows with first name Antonio AND hotmail email addresses
data.loc[data['email'].str.endswith("gmail.com") & (data['first_name'] == 'Antonio')] 
 
# select rows with id column between 100 and 200, and just return 'postal' and 'web' columns
data.loc[(data['id'] > 100) & (data['id'] <= 200), ['postal', 'web']] 
 
# A lambda function that yields True/False values can also be used.
# Select rows where the company name has 4 words in it.
data.loc[data['company_name'].apply(lambda x: len(x.split(' ')) == 4)] 
 
# Selections can be achieved outside of the main .loc for clarity:
# Form a separate variable with your selections:
idx = data['company_name'].apply(lambda x: len(x.split(' ')) == 4)
# Select only the True values in 'idx' and only the 3 columns specified:
data.loc[idx, ['email', 'first_name', 'company']]
```

Penggunaan ix juga bisa untuk mengambil data pada dataframe, namun untuk kasus yang lebih kompleks gunakan loc atau iloc supaya mengurangi hasil yang tidak diharapkan.  
  
Setting data dengan menggunakan loc
```py
df.loc[df['id'] > 2000, "first_name"] = "John"
```

Menangani data duplikat [sumber](https://towardsdatascience.com/finding-and-removing-duplicate-rows-in-pandas-dataframe-c6117668631f)
```py
# Menghitung jumlah data duplicate pada dataframe
df.duplicated().sum()

# Menghitung jumlah data duplicate pada kolom
df.duplicated(subset=['first_name'], ['last_name']).sum()

# Mengekstrak data duplikat
df.loc[df.duplicated(), :]

# Menghapus data terduplikat, baca juga tentang keep
df.drop_duplicates(keep='first', inplace=True)

```

Convert data frame
```py
df.to_csv
df.to_dict
```

[Vlookup](https://www.geeksforgeeks.org/how-to-do-a-vlookup-in-python-using-pandas/)


## Processing CSV in Practice
CSV 1
```csv
"code_module","code_presentation","id_student","gender","region","highest_education","imd_band","age_band","num_of_prev_attempts","studied_credits","disability","final_result"

"AAA","2013J","11391","M","East Anglian Region","HE Qualification","90-100%","55<=","0","240","N","Pass"

"AAA","2013J","28400","F","Scotland","HE Qualification","20-30%","35-55","0","60","N","Pass"
```

CSV2
```csv
"id_assessment","id_student","date_submitted","is_banked","score"

"1752","11391","18","0","78"

"1752","28400","22","0","70"
```

CSV3
```csv
"code_module","code_presentation","id_assessment","assessment_type","date","weight"

"AAA","2013J","1752","TMA","19","10"

"AAA","2013J","1753","TMA","54","20"
```

Initialization
```py
import pandas as pd

# to random name later on
import names
```

Remove Duplication on CSV1
```py
file_name = "Backup\\Original Data\\studentInfo.csv"

def removeDuplicate(file_name=file_name, output_file="studentData.csv") :
  df = pd.read_csv(file_name)

  # using only these tables
  df = df[["id_student", "gender", "region", "highest_education", "imd_band", "age_band", "disability"]]

  # dropping tables based on similarity of id_student
  df.drop_duplicates(subset="id_student", inplace=True)
  df.to_csv(output_file, index=False)
```

Add random name to students, because the csv contains anonymous data
```py
def addName(file_name="studentData.csv", output_file="studentData.csv") :
  df = pd.read_csv(file_name)

  # inserting column name with the value of "Change"
  if "name" not in df.head(0) :
    df.insert(1, column="name", value="Change")
    
  pointer = 0

  # df.shape returns (total_row, total_column)
  row_count = df.shape[0]


  for i in range(row_count):
    if df.loc[i, "name"] == "Change":
      if df.loc[i, "gender"] == "M":
        name = names.get_full_name(gender="male")
      else :
        name = names.get_full_name(gender="female")
      
      df.loc[i, "name"] = name
      pointer += 1
      print(pointer, " / ", row_count)
    
    else:
      pointer += 1
      print(pointer, " / ", row_count)
      continue

  df.to_csv(output_file, index=False)
```

Combining CSV2 and CSV3
```py
df1, df2 = pd.read_csv(file_name), pd.read_csv(file_name2)

  if "is_banked" in df1.head(0):
    df1.pop("is_banked")

  # merging 2 data frames
  inner_join = pd.merge(df1, df2, on="id_assessment", how="inner")

  # reorder columns
  reorder = ["code_module", "code_presentation", "id_assessment", "assessment_type", "id_student", "date", "date_submitted", "weight", "score"]

  inner_join = inner_join[reorder]

  # rename date column into deadline
  if "date" in inner_join.head(0):
    inner_join = inner_join.rename(columns={"date": "deadline"})
  
  inner_join.to_csv(output_file, index=False)
```

Adding random module name
```py
def addModuleName(file_name="assessmentDetails.csv", output_file="assessmentDetails.csv"):
  df = pd.read_csv(file_name)
  module_codes = {
    "AAA": "Big Data Analysis",
    "BBB": "Introduction to Machine Learning",
    "CCC": "Information Security",
    "DDD": "Algorithm and Data Structure",
    "EEE": "Blockchain Technology",
    "FFF": "Business Analysis",
    "GGG": "Software Development"
  }

  if "module_name" not in df.head(0):
    df.insert(1, column="module_name", value="Change")

  row_count = df.shape[0]

  for i in range(row_count):
    if df.loc[i, "module_name"] == "Change":
      key = df.loc[i, "code_module"]
      df.loc[i, "module_name"] = module_codes[key]

  df.to_csv(output_file, index=False)
```

## Convert to JSON (for nosql) in Practice
Init
```py
import pandas as pd
import json

file_name1 = 'studentData.csv'
file_name2 = 'assessmentDetails.csv'

df1, df2 = pd.read_csv(file_name1), pd.read_csv(file_name2)
```

Implement nesting and convert assessmentDetails.csv into JSON format.
```py
def assessmentJson(df=df2, out='assessmentDetails.json'):
  # nesting code_module and module_name to records
  df['module'] = df[['code_module', 'module_name']].to_dict('records')
  out = df[['module', 'code_presentation', 'id_assessment', 'assessment_type', 'id_student', 'deadline', 'date_submitted', 'weight', 'score']].to_json(out, orient='records', indent=4)
```

Convert studentData.csv into JSON format.
```py
def studentJson(df=df1, out='studentData.json'):
  out = df[['id_student', 'name', 'gender', 'region', 'highest_education', 'imd_band', 'age_band', 'disability']].to_json(out, orient='records', indent=4)
```

Implement embedding for two different data frames, based on the key_id
```py
def merging(df1=df1, df2=df2): 
  pointer = 0
  row_count = df1.shape[0]
  list_json = []

  for i in range(row_count):
    # searching for the key_id based on the id_student column
    key_id = df1.iloc[i]['id_student']
    scan = df2.loc[df2['id_student'] == key_id]
    scan2 = df1.loc[df1['id_student'] == key_id]

    scan = scan[['code_module', 'module_name', 'code_presentation', 'id_assessment', 'assessment_type', 'deadline', 'date_submitted', 'weight', 'score']].to_dict('records')

    out = scan2[['id_student', 'name', 'gender', 'region', 'highest_education', 'imd_band', 'age_band', 'disability']].to_dict('records')

	# embed information from assessmentDetails.csv into assessment (in json format)
    dict_scan = { }
    dict_scan['assessments'] = scan

	# create exception handling for unfinished students (assessment not complete)
    try :
      out[0].update(dict_scan)
    except IndexError :
      list_json.append(out[0])
      out = { }
    
    list_json.append(out[0])
    out = { }
    
    pointer += 1
    print(pointer, "/", row_count)


  # dumps the list created on the for loop above into list of data  
  list_json = json.dumps(list_json, indent=4)
  print(list_json)

  # write the data inside embedding.json file
  with open('embedding.json','w') as file:
    file.write(list_json)
```

