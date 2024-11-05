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

### **Exercice 8**
> Determine whether a sentence is a pangram.

```bash
main () {
    (( ${#*} != 1 )) && echo "Usage: pangram.sh <sentence1>" && exit 1
    SENTENCE="${1,,}" ## to lowercase
    LIST="abcdefghijklmnopqrstuvwxyz"
    COUNT=0
    for (( k=0; k<${#SENTENCE}; k++ )); do ## for each character in sentence
        for (( i=0; i<${#LIST}; i++ )); do ## look for in the alphabet
            ## if in the alphabet, increment count & pop the letter out of the alphabet
            if [[ "${SENTENCE:k:1}" == "${LIST:i:1}" ]]; then
                LIST="${LIST::i}${LIST:i+1}"
                ((COUNT++))
                break ## don't need to continue comparing to other letters in the alphabet
            fi
        done
        if [[ COUNT -eq 26 ]]; then
            echo "true"
            exit 0
        fi
    done
    echo "false"
}
main "$@"
```
> - Pop character at index from string.
> - Break out of loop.

*Other more elegant solution*
```bash
alphabet="abcdefghijklmnopqrstuvwxyz" # set the alphabet
pangram_candidate="${1,,}"            # set the candidate
# get outta here letters && I love Bash built-ins
zero_check="${alphabet//[$pangram_candidate]/}"
# if zero_check is empty then the candidate was a pangram
[[ -z $zero_check ]] && echo 'true' || echo 'false'
```

### **Exercice 9**
> What will Bob reply ?  

```bash
main () {

    # 1. ? = Sure
    # 2. ABC = Whoa
    # 3. ABC + ? = Calm down
    # 4. "   " = Fine
    # 5. else = Whatever

    CAPS=${1^^}
    [[ "$1" =~ [A-Za-z] ]] && letters=true || letters=false
    trimmed_input="${1%"${1##*[![:space:]]}"}"

    if [[ "${trimmed_input: -1}" == "?" ]]; then
        if [[ $1 == "$CAPS" ]] && $letters; then
            echo "Calm down, I know what I'm doing!"
        else
            echo "Sure."
        fi
        exit 0
    fi

    [[ ! $1 = *[![:space:]]* ]] && echo "Fine. Be that way!" && exit 0
    [[ $1 == "$CAPS" ]] && $letters && echo "Whoa, chill out!" && exit 0
    echo "Whatever."
}

main "$@"
```

> - Check if string has alphabetical characters.
> - Check last non-whitespace character.
> - Check empty/blank string. *(⚠️ special characters like \t \n \r)*

### **Exercice 10**
> Compute the Scrabble score of a word.

```bash
main () {
    SCORE=0
    WORD="${1^^}"

    if [ ${#1} -gt 0 ]
    then
        for((i=0;i<${#1};i++)); do
            [[ "${WORD:i:1}" =~ [AEIOULNRST] ]] && SCORE=$((SCORE + 1)) && continue
            [[ "${WORD:i:1}" =~ [DG] ]] && SCORE=$((SCORE + 2)) && continue
            [[ "${WORD:i:1}" =~ [BCMP] ]] && SCORE=$((SCORE + 3)) && continue
            [[ "${WORD:i:1}" =~ [FHVWY] ]] && SCORE=$((SCORE + 4)) && continue
            [[ "${WORD:i:1}" =~ [K] ]] && SCORE=$((SCORE + 5)) && continue
            [[ "${WORD:i:1}" =~ [JX] ]] && SCORE=$((SCORE + 8)) && continue
            [[ "${WORD:i:1}" =~ [QZ] ]] && SCORE=$((SCORE + 10)) && continue
        done
    fi
    echo "$SCORE"
}

main "$@"
```

> - Character in list of characters *(Regex)*.

### **Exercice 11**
> Calculate the number of grains of wheat on a chessboard given that the number on each square doubles.

```bash
main () {
    (( ${#*} != 1 )) && echo "Usage: grains.sh <number1>" && exit 1

    if (($1 >= 1 && $1 <= 64)); then
        printf "%llu\n" $((2 ** $(($1 - 1))))
    elif [[ $1 == "total" ]];then
        printf "%llu\n" $((2 ** 64-1))
    else
        echo "Error: invalid input" && exit 1
    fi
}

main "$@"
```

> - Handling 64 bit integer limitation.

## 📁 **Ressources**  
- [How to learn bash.](https://exercism.org/docs/tracks/bash/learning)  
- [Bash shortcuts for maximum productivity.](https://skorks.com/2009/09/bash-shortcuts-for-maximum-productivity/)  
- [Bash scripting cheatsheet.](https://devhints.io/bash)  
- [Good bash tutorials.](https://www.baeldung.com/linux/bash-variable-is-numeric)  