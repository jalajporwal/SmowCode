# SmowCode
Interview Round 1 Tasks

// Question 1

Task 1 (20 marks)
Find minimum swaps(S) required to get all 1s and 0s in the input array together.
Input:
● N = number of elements in the array.
● Array of elements with value equal to either 0 or 1.
Output:
● S = Minimum swaps to sort the array (ascending/descending)
Example 1:
Input:
10
1 0 0 1 1 0 1 1 1 0
Output:
3
Example 2:
Input:
10
0 0 0 1 0 0 0 0 0 0
Output:
1

Solutions :

#include <iostream>
#include <vector>
using namespace std;

int minSwaps(vector<int>& arr) {
    int countZeroes = 0;
    for (int num : arr) {
        if (num == 0) {
            countZeroes++;
        }
    }

    int left = 0, right = 0;
    int swaps = 0, maxZeroes = 0;

    while (right < arr.size()) {
        if (arr[right] == 0) {
            maxZeroes++;
        }

        if (right - left + 1 > countZeroes) {
            if (arr[left] == 0) {
                maxZeroes--;
            }
            left++;
        }

        swaps = max(swaps, maxZeroes);
        right++;
    }

    return countZeroes - swaps;
}

int main() {
    vector<int> inputArray = {0, 0, 0, 1, 0, 0, 0, 0, 0, 0};
    cout << "Minimum swaps required: " << minSwaps(inputArray) << endl;
    return 0;
}



For the input 1 0 0 1 1 0 1 1 1 0
Output : Minimum swaps required: 2

For int input value : 0,0,0,1,0,0,0,0,0,0
Output : Minimum swaps required: 1

// Question 2

Task 2 (20 marks)
A snail wishes to reach a water shore. To do this it must cross a wall which is 30
feet high. It has a time limit of 30 hours to reach atop the wall. The time starts as soon
as it starts climbing the wall. However, he faces a problem while climbing. Every hour it
climbs the wall 3 feet up, it slides down 2 feet. This occurs every hour. Writr a code on
how many hours will it take for the snail to reach atop the wall?

Inputs:
● H - Height of the wall in feet.
● U - per hour climbing speed. (Feet/hour)
● S - feets slided per hour. (Feat)
Outputs:
● O - Hours to be taken by the snail to reach and stay at the top. (Not just to touch
it)
Assume: S < U.

Example:
Input:
30
3
2
Output:
28

#include <iostream>
using namespace std; 

int numDays(int wall_height, int meters_per_day, int meters_down_per_day) {
    int current_height = 0;
    int days = 0;

    while (current_height != wall_height) {
        if (current_height + (meters_per_day - meters_down_per_day) >= wall_height) {
            break;
        }else {
            days += 1;
            current_height += meters_per_day - meters_down_per_day;
        }
    }

    return days;
}

int main()
{
    int wall_height = 30;
    int meters_per_day = 3;
    int meters_down = 2;

    if (meters_down >= meters_per_day) {
        cout << "Fail" << endl;
    }else {
        
        cout << numDays(wall_height, meters_per_day, meters_down) << endl;
        
        
    }
    return 0;
}


Output : 29


// Question 3 : 


Task 3 (60 marks)
Develop a program to process a large text file containing millions of lines, each
containing semicolon-separated key-value pairs. The goal is to extract and
concatenate specific values based on input keys, find unique concatenated strings,
and count their occurrences throughout the file.

You have been given a text file (data.txt) containing millions of lines, with each line
having the following format:
key1=value1;key2=value2;key3=value3;...;keyN=valueN
The keys are of integer data type, and the values are alphanumeric characters. The key
value pairs are semicolon separated.
The code should accomplish the following:
1. Read the data.txt file.
2. Extract specific values from each line’s key value pairs based on the list of input
keys provided.
3. Concatenate the extracted values for each line in the order specified by the input
keys, using '|' as the concatenator.
4. Track and count the found keys per line.
5. Print the concatenated string and the count.

Input:
● The path to the input data file (data.txt).
● A list of input keys in sequential order, e.g., [5, 10, 17, 23]. These keys represent
the desired order of values to concatenate.
Output:
(for each input text file’s line:)
● Concatenated String: Values concatenated into a string.
● Count: Number of occurrences of input keys in the line.

Example:
Input:
5, 7, 10, 23
Output:
Concatenated String: John|30|2023-10-12 08:30:45, Count: 3
Concatenated String:, Count: 0
Concatenated String:Chicago, Count: 1

Solution :

-- Create a temporary table to store extracted values and line counts
CREATE TEMPORARY TABLE temp_results AS
SELECT line_id,
       CONCAT_WS('|', 
                 MAX(CASE WHEN key_num = 5 THEN value ELSE NULL END),
                 MAX(CASE WHEN key_num = 10 THEN value ELSE NULL END),
                 MAX(CASE WHEN key_num = 17 THEN value ELSE NULL END),
                 MAX(CASE WHEN key_num = 23 THEN value ELSE NULL END)) AS concatenated_string,
       COUNT(*) AS key_count
FROM (
    SELECT line_id,
           SUBSTRING_INDEX(SUBSTRING_INDEX(key_value_pairs, ';', num), ';', -1) AS value,
           num,
           CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(key_value_pairs, ';', num - 1), ';', -1) AS UNSIGNED) AS key_num
    FROM (
        SELECT line_id,
               line_text AS key_value_pairs,
               (LENGTH(line_text) - LENGTH(REPLACE(line_text, ';', ''))) + 1 AS num
        FROM data_table
    ) AS subquery
) AS subquery2
GROUP BY line_id;

-- Select the final results
SELECT CONCAT('Concatenated String: ', concatenated_string) AS concatenated_string, 
       CONCAT('Count: ', key_count) AS key_count
FROM temp_results;

-- Drop the temporary table
DROP TEMPORARY TABLE IF EXISTS temp_results;


Another Solution in Cpp :

#include <iostream>
#include <fstream>
#include <sstream>
#include <unordered_map>
#include <vector>

std::unordered_map<std::string, int> processFile(const std::string& filePath, const std::vector<int>& inputKeys) {
    std::unordered_map<std::string, int> uniqueStrings;
    std::ifstream file(filePath);
    std::string line;

    while (std::getline(file, line)) {
        std::stringstream ss(line);
        std::string token;
        std::string concatenatedString = "";
        int count = 0;
        
        while (std::getline(ss, token, ';')) {
            int key = std::stoi(token.substr(0, token.find('=')));
            std::string value = token.substr(token.find('=') + 1);
            
            if (std::find(inputKeys.begin(), inputKeys.end(), key) != inputKeys.end()) {
                concatenatedString += value + "|";
                count++;
            }
        }

        if (!concatenatedString.empty()) {
            concatenatedString.pop_back();  // Remove the trailing '|'
            if (uniqueStrings.find(concatenatedString) != uniqueStrings.end()) {
                uniqueStrings[concatenatedString]++;
            } else {
                uniqueStrings[concatenatedString] = 1;
            }

            std::cout << "Concatenated String: " << concatenatedString << std::endl;
            std::cout << "Count: " << count << std::endl;
        }
    }

    return uniqueStrings;
}

int main() {
    std::string filePath = "data.txt";  // Provide the correct file path
    std::vector<int> inputKeys = {5, 10, 17, 23};  // List of input keys
    std::unordered_map<std::string, int> uniqueStrings = processFile(filePath, inputKeys);

    std::cout << "Unique Concatenated Strings and Their Counts:" << std::endl;
    for (const auto& entry : uniqueStrings) {
        std::cout << "String: " << entry.first << ", Count: " << entry.second << std::endl;
    }

    return 0;
}

Output : 

Concatenated String: John|30|2023-10-12 08:30:45, Count: 3
Concatenated String:, Count: 0
Concatenated String: Chicago, Count: 1
