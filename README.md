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
> - `||` or ***OR*** runs the command on the right if the one on the left returns ***false***.
> - the `%` operator computes the remainder of the division : returns `0 (false)` if  divisible, else returns a `non-zero value (true)`. 

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

### **Exercice 12**
> Determine whether a number is valid per the Luhn formula.

```bash
main () {
    # Check if digits only (! whitespaces)
    # start sum
    # parse from right
    # if k%2 = 0, sum += string[k] * 2  (-9 if>9)
    # if sum%10 -> true

    isLuhn=false
    string="${1// /}"

    if [[ ${#string} -gt 1 ]];then

        if [[ "${1// /}" =~ ^[0-9]+$ ]]; then

            sum=0

            for (( i=0; i<${#string}; i++ )); do
                toAdd=${string:(-i-1):1}
                ! (( i % 2 )) || toAdd=$((${string:(-i-1):1} * 2))
                ! (( toAdd > 9)) || toAdd=$((toAdd - 9))

                sum=$(( sum + toAdd))
            done

            (( sum % 10)) || isLuhn=true

        fi
    fi
    echo "$isLuhn"
}

main "$@"
```

> How does `[[ "${count// /}" =~ ^[0-9]+$ ]]` work ?
> 1. `${count// /}`:  
> - This is a parameter expansion. It removes all spaces (`" "`) from the value of the variable `count`. The syntax `//` indicates replacing all occurrences of the space with nothing (i.e., removing it).
> 2. `=~`:  
> - This operator checks whether the string on the left side matches the regular expression on the right side.
> 3. `^[0-9]+$`:
> 	- This is a regular expression:
> 		- `^` means the start of the string.
> 		- `[0-9]` matches any digit (0 through 9).
> 		- `+` means one or more of the preceding element (digits in this case).
> 		- `$` means the end of the string.
> 		
> 	- So, `^[0-9]+$` matches a string that consists entirely of one or more digits, with no other characters.

*Other solution*
```bash
quit() {
	echo false
	exit
}
str="$*"
number="${str// /}"
[[ $number =~ ^[0-9]*$ ]] || quit
[[ ${#number} -gt 1 ]] || quit
declare -i sum=0 digit
for ((i = 1; i <= ${#number}; i++)); do
	digit=${number:$((-i)):1}
	((i % 2 == 0)) && ((digit *= 2)) && ((digit > 9)) && ((digit -= 9))
	((sum += digit))
done
((sum % 10 == 0)) && echo true || echo false
```

## **Exercice 13**
> Create an implementation of the atbash cipher, an ancient encryption system created in the Middle East.

```bash
get_char_position() {
    # Get position of character in alphabet.
    local char="$1"
    char=$(echo "$char" | tr '[:upper:]' '[:lower:]')
    echo "$(( $(printf "%d" "'$char") - 96 ))"
}


main () {
    # Add space when encoding.
    # Push character at (-index) in alphabet
    
    (( ${#*} != 2 )) &&  [[ "$1" =~ ^(decode|encode)$ ]] && echo "Usage: atbash_cipher.sh <encode/decode> <sentence1>" && exit 1

    Plain="abcdefghijklmnopqrstuvwxyz"
    string="${2// /}" && string=$(echo "$string" | tr -d '[:punct:]') && string="${string,,}"
    result=""

    for (( i=0; i<${#string}; i++ )); do
        if [[ "${string:i:1}" =~ [a-z] ]];then

            [[ "${1}" == "encode" ]] && (( i != 0 && i % 5 == 0 )) && result+=" "
            position=$(get_char_position "${string:i:1}")
            result+="${Plain:(-$position):1}"

        else result+="${string:i:1}"
        fi
    done
    echo "$result"
}

main "$@"
```

> - Call a function.
> - Some more experience with conditions.

*Other elegant solution*
```bash
main () {
    result=$( tr "abcdefghijklmnopqrstuvwxyz" "zyxwvutsrqponmlkjihgfedcba" <<< ${2,,} | tr -d " .," )
    [ "$1" == "encode" ] && result=$(fold -w5 <<< $result)
    echo $result
}
main "$@"
```

## **Exercice 14**
> Reverse a given string.

```bash
main () {
    (( ${#*} != 1 )) && echo "Usage: reverse_string.sh <string1>" && exit 1
    reversed=""
    for (( i=0; i<${#1}; i++)); do
        reversed+="${1:(-i-1):1}"
    done
    echo "$reversed"
}

main "$@"
```

*Other solution*
```bash
echo "$1" | rev
```

> `rev` : command to reverse lines character-wise. *(also works with files to reverse each line)*

## **Exercice 15**
> Determine whether a given year is a leap year.

```bash
main () {
    (( ${#*} != 1 )) || [[ ! "$1" =~ ^[0-9]+$ ]] && echo "Usage: leap.sh <year>" && exit 1
    (($1 % 4 == 0)) && (( ( ($1 % 100 == 0) && ($1 % 400 == 0) ) || !($1 % 100 == 0) )) && echo "true" || echo "false"
}

main "$@"
```

> - Check if integer argument.
> - Some more experience with conditions.

*Other solutions*
- Better boolean expression :
```bash
(( year % 4 == 0 && (year % 100 != 0 || year % 400 == 0) ))
```
- Ternary operator of Boolean expressions :
```bash
(( year % 100 == 0 ? year % 400 == 0 : year % 4 == 0 ))
```
- External tools :
```bash
year=$1
next_day=$(date -d "$year-02-28 + 1 day" '+%d')
[[ $next_day == "29" ]] && echo true || echo false
```

***Which one to choose ?***
- The chain of Boolean expressions should be the most efficient, as it proceeds from the most likely to least likely conditions. It has a maximum of three checks. It is the most efficient approach when testing a year that is not evenly divisible by `100` and is not a leap year, since the most likely outcome is eliminated first.
- The ternary operator has a maximum of only two checks, but it starts from a less likely condition.
- Using external tools to do `datetime` addition may be considered a "cheat" for the exercise, and it will be slower than the other approaches.


## 📁 **Ressources**  
- [How to learn bash.](https://exercism.org/docs/tracks/bash/learning)  
- [Bash shortcuts for maximum productivity.](https://skorks.com/2009/09/bash-shortcuts-for-maximum-productivity/)  
- [Bash scripting cheatsheet.](https://devhints.io/bash)  
- [Good bash tutorials.](https://www.baeldung.com/linux/bash-variable-is-numeric)  