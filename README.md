# Portfolio_EIS
# About
Elektrochemical Impedance spectroscopy (EIS) is based on activating a material with a small electromagnetic signal selected from a specific frequency range and measuring the electrical response of the examined sample with a given geometry, analyzing its physicochemical properties.
Unfortunately, the program used in impedance spectroscopy saves temperature data multiple times at close intervals. As a result, we end up with a list of text files, and our task is to check each file to find temperatures closest to the desired ones. Typically, for my master's work, I had around 400 text files to examine. The list of text files looks like this:

<img src="https://github.com/RieForGitHub/Portfolio_EIS/blob/main/Photo1.jpg" width="550">

Here's an example of the content inside a text file:

<img src="https://github.com/RieForGitHub/Portfolio_EIS/blob/main/Photo2.jpg" width="900">

# Goals
The main purpose of my work was to speed up my analysis, make it more precise (reducing human errors), and optimize the process to free up time for other important tasks, such as drawing conclusions. To achieve this, I developed a Python program that identifies temperatures with the closest values at each step. At the end of its operation, the program also deletes unnecessary files, enabling me to work more efficiently and start analyzing data with OriginLab.

# Program
Input: directory path 

```# -*- coding: utf-8 -*-
import os
directory_path="C:\\Users\Justyna\Desktop\Temperaturowe"

# creating some variables used in program
file_names=os.listdir(directory_path)
common_name = os.path.commonprefix(file_names)
last_file = len(file_names)
name_line='Temp.'
zapis=[]
zipy=[]
line_as_float=[]
tym=[]
tym1=[]
usun=[]

# function for searching closest value in 'temperatures.txt'
def takeClosest(num,looking_in_list):
    return(min(looking_in_list,key=lambda x:abs(x-num)))

def findSteps(leftRange, rightRange, minimum_difference):
    with open('temperatures.txt', 'r') as file:
        lines = file.readlines()
        for i in range(leftRange-1, rightRange + 1):
            temperature_1 = float(lines[i].split()[0])
            temperature_2 = float(lines[i+1].split()[0]) 
            difference = abs(temperature_2 - temperature_1) 
            if difference < minimum_difference: 
                i=i+1
            else:
                result = int(difference) 
        return result
        
# creating file to look with all temperatures and filenames 
for i in range(last_file):
    try:
        with open(common_name+str(i)+'.txt','r') as file:
            lines=file.readlines()
            for line in lines:
                if name_line in line:
                    with open('temperatures.txt','a') as file:
                        file.write(line[29:36]+'  '+ common_name+str(i)+'\n')
                    i+=1
    except FileNotFoundError:        
        if True:
            i+=1 

# searching lowest (beginning temperature) and highest temperature (in heating)
# searching in which file is beginning of cooling
max_temperature = None
max_line = ''
with open('temperatures.txt','r') as file:
    first_line = file.readline()
    beginning_temperature = int(float(first_line.split()[0]))
with open('temperatures.txt','r') as file:
    lines = file.readlines()
    for line in lines:
        temporary_t = int(float(line.split()[0]))
        if max_temperature is None or temporary_t > max_temperature:
            max_temperature = temporary_t
            max_line = line.strip()
max_line_number = (max_line.split(common_name)[-1]).strip()

# searching heating and cooling step (step between next closest temperature)
heating_step = findSteps(beginning_temperature, max_temperature, 1)
cooling_step = findSteps(max_temperature + 2, last_file - 6, 5)
print("Sprawdzenie: ", heating_step)
print("Sprawdzenie: ", cooling_step)

# using closest_value function to find heating and save it
with open('temperatures.txt','r') as file:
    lines=file.readlines()
    for number in range(beginning_temperature-1, max_temperature + 1, 
                        heating_step): 
        for line in lines:
            line_as_float.append(float(line[0:7]))
        closest_value=takeClosest(number, line_as_float)
        closest_line=lines[line_as_float.index(closest_value)]
        zipy=closest_line[9:15]
        wynik=zipy.strip()+'  '+str(closest_value)
        zapis.append(wynik)
        usun.append(zipy)
    
# using closest_value function to find cooling and save it
line_as_float=[]
with open('temperatures.txt','r') as file:
    lines=file.readlines()
    for number in range(-1, max_temperature - cooling_step + 1, 
                        cooling_step): 
        for line in lines:
            line_as_float.append(float(line[0:7]))
        closest_value=takeClosest(number, line_as_float)
        closest_line=lines[line_as_float.index(closest_value)]
        zipy=closest_line[9:15]
        wynik=zipy.strip()+'  '+str(closest_value)
        tym.append(wynik)
        tym1.append(zipy)
tym.reverse()
tym1.reverse()

# saving interesting lists for one file
with open('calculated.txt','w') as file:
    for line in zapis:
        file.write(line+'\n') 
    for line in tym:
        file.write(line+'\n')
# %%
# deleting rest files
usun=[name.strip() for name in usun]
all_files=os.listdir(directory_path)
for file_name in all_files:
    if file_name[:-4] not in usun:
        os.remove(os.path.join(directory_path,file_name))
```


# How The Program Works
The program operates in steps of 5 degrees Celsius, searching for the closest temperatures at each interval. For instance, finding the closest value for -150 degrees Celsius looks like this:
<img src="https://github.com/RieForGitHub/Portfolio_EIS/blob/main/Photo3.jpg" width = "200">

The results of running this program are as follows:
<img src="https://github.com/RieForGitHub/Portfolio_EIS/blob/main/Photo4.jpg" width = "550">
