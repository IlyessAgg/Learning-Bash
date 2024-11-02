# Bash Learning

> Leveling up my programming skills.  

<p align='center'>
  <img src="./imgs/header.png" width="415" height="197"/>
</p>

<p style = 'line-height: 20px'>
  I use <a href="https://exercism.org/"><img src = './imgs/exercism.png' style='vertical-align: bottom' width="20" /> Exercism</a> which is an open-source, free coding platform that offers code practice and mentorship on 74 different programming languages.
</p>

## Setup

Currently, I'm using it to learn **Bash**. I've set it up on `WSL2` and editing via `Visual Studio Code` using the following guides:

- [VSCode : Remote development in WSL.](https://code.visualstudio.com/docs/remote/wsl-tutorial)
- [Exercism : Learn how to solve exercises on your local machine](https://exercism.org/docs/using/solving-exercises/working-locally)

## Bash

> Currently learning **Bash**. I'm only recording my solutions and some personal notes but I might save each exercice separately if it gets too complicated or if I start another programming language.

### **Exercise 1**
```bash
echo "Hello, World!"
```

### **Exercise 2**
```bash
echo "One for ${1:-you}, one for me."
```

> - Access arguments.
> - `1` refers to the first argument.
> - You can define a default value with the syntax : `VAR=${1:-DEFAULTVALUE}` 

### **Exercice 3**
```bash
if [[ $# -eq 1 ]]
then
    echo "Hello, $1"
else
    { echo "Usage: error_handling.sh <person>"; exit 1; }
fi
```

> - Set a condition relating the number of arguments.

### **Exercice 4**
```bash
(( $1 % 3 )) || result+="Pling"
(( $1 % 5 )) || result+="Plang"
(( $1 % 7 )) || result+="Plong"
echo "${result:-$1}"
```

> - Set an arithmetic condition.

> _the logic on line 1 to 3 is to calculate the remainder of the number with 3, 5 or 7. Per the bash manpage, if what's inside ((...)) evaluates to a non-zero value, the return status is 0, otherwise the return status is 1. When the return status is 1, the "||" gets evaluated._

### **Exercice 5**
> Calculate the Hamming distance between two DNA strands.
```bash
hamming=0

if [[ $# -ne 2 ]]
then
    { echo "Usage: hamming.sh <string1> <string2>"; exit 1; }
fi

if [ ${#1} -eq ${#2} ]
then
    for((i=0;i<${#1};i++)); do
        if [[ "${1:i:1}" != "${2:i:1}" ]];
        then
            ((hamming++))
        fi
    done
echo "$hamming"
else
    { echo "strands must be of equal length"; exit 1; }
fi
```

> - For loop.
> - Get character from string.

*Other solution without so many if statements*
```bash
main () {
  (( ${#*} < 2 )) && echo "Usage: hamming.sh <string1> <string2>" && exit 1
  (( ${#1} != ${#2} )) && echo "strands must be of equal length" && exit 1
  local diff=0
  local i
  for (( i=0; i<${#1}; i++ )); do
    [ "${1:$i:1}" != "${2:$i:1}" ] && (( diff++ ))
  done
  echo $diff
}
main "$@"
```

### **Exercice 6**
> Convert a phrase to its acronym.
```bash
main () {
    (( ${#*} != 1 )) && echo "Usage: acronym.sh <string1>" && exit 1

    RESULT=""
    IFS=' -*_'
    read -ra elements <<< "$1"

    for element in "${elements[@]}"; do
        RESULT+="${element:0:1}"
    done
    
    echo "${RESULT^^}" ## uppercase the string.
}

main "$@"
```

> - Splitting a string.

### **Exercice 7**
> Determine whether a number is an Armstrong number.
```bash
main(){
    (( ${#*} != 1 )) && echo "Usage: armstrong_numbers.sh <number1>" && exit 1
    
    SUM=0

    for (( i=0; i<${#1}; i++ )); do
        SUM=$((SUM + $((${1:$i:1} ** ${#1}))))
    done

    if [[ $1 -eq $SUM ]];then
        echo "true"
    else
        echo "false"
    fi
}

main "$@"
```

> - Parsing digits of a number.
> - Sum : `$((num1 + num2))`
> - Power : `$((2 ** 4))`


## 📁 **Ressources**  
- [How to learn bash.](https://exercism.org/docs/tracks/bash/learning)  
- [Bash shortcuts for maximum productivity.](https://skorks.com/2009/09/bash-shortcuts-for-maximum-productivity/)  
- [Bash scripting cheatsheet.](https://devhints.io/bash)  
- [Good bash tutorials.](https://www.baeldung.com/linux/bash-variable-is-numeric)  