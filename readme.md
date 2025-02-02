### **Report: Data Augmentation for Employee Chatbot**

#### **1. Introduction**  

The objective of this project was to enhance a dataset of employee queries to improve the performance of a chatbot. The original dataset consisted of **1,022 rows** with three columns: `user_query`, `intent`, and `solution`.  

After evaluating multiple data augmentation techniques, the **GPT-4o Mini model** (**Rogue-Rose-103b-v0.2**, via OpenRouter API) was selected for its ability to generate high-quality and diverse queries. Additionally, the **Back Translation technique** was employed to further enrich the dataset.  

The final augmented dataset was divided into three versions:  
- **Version 1**: `13,225 rows`  
- **Version 2**: `14,165 rows`  
- **Version 3**: `14,952 rows`  

To support a **French-language chatbot**, the dataset was translated into French using the `deep-translator` library. This process ensured that the dataset is available in both **English and French**, making it suitable for multilingual chatbot training.  

---

### *Installation*
To install all required libraries, we create a requirements.txt file and to install them run this command:

```bash
    pip install -r requirements.txt
```
---

#### **2. Techniques Evaluated**
Several techniques were initially explored for data augmentation, but only **GPT-4o Mini** was used for the final workflow due to its superior performance.

##### **2.1 Paraphrasing**
- **Objective**: Generate variations of existing queries while preserving the `intent` and `solution`.
- **Tools Used**: `nlpaug` library with BERT-based paraphrasing.
- **Challenges**:
  - **Grammar Issues**: Some paraphrased queries had grammatical errors (e.g., "I are having trouble").
  - **Irrelevant Content**: Occasionally, the paraphrased queries were nonsensical or irrelevant.
- **Solutions**:
  - Integrated *grammar correction* using language-tool-python.
  - Added *validation* to filter out irrelevant outputs.
- **Results**:
  - Generated **8,176 rows** across 3 attempts (75 minutes per attempt).
  - **Not used in the final dataset** due to quality issues.

##### **2.2 Back-Translation**
- **Objective**: Translate queries into an intermediate language (e.g., French) and back to English to generate diverse variations.
- **Tools Used**: `deep-translator` library
- **Results**:
  - Generated **1022 rows** in 32 minutes.

##### **2.3 Synthetic Data Generation**
- **Objective**: Generate new queries based on the `intent` and `solution` using advanced language models.
- **Tools Used**:
  - *GPT-2*: Initial attempts with GPT-2 produced generic or irrelevant outputs.
  - *T5*: Improved results with T5, but some outputs were still repetitive or nonsensical.
  - *OpenRouter API*:
    - *DeepSeek-R1*: Produced high-quality outputs but limited diversity.
    - *GPT-4o Mini*: Achieved the best balance of quality and diversity.
    - *Rogue-Rose-103b-v0.2*: Achieved the best balance of quality and diversity
- **Challenges**:
  - *Irrelevant Outputs*: GPT-2 and T5 sometimes generated irrelevant or repetitive queries.
  - *API Limitations*: OpenRouter API had occasional rate limits or errors.
- **Solutions**:
  - Refined *prompts* to guide the model better.
  - Added *validation* to filter out irrelevant outputs.
  - Switched to *GPT-4o Mini* and *Rogue-Rose-103b-v0.2* for better performance and diversity.
- **Results**:
    1. *GPT-4o Min*
        - Generated **13,798 rows** in 140 minutes.
        - Achieved the best balance of **quality** and **diversity**.
        - **Selected for the final dataset**.
    2. *Rogue-Rose-103b-v0.2*
        - Generated **961 rows** in 39 minutes.
        - Achieved the best balance of **quality** and **diversity**.
        - **Selected for the final dataset**. 
---

#### **3. Final Workflow: GPT-4o Mini/ Rogue Rose**
The final workflow exclusively used the **GPT-4o Mini model** and **Rogue Rose-103b-v0.2 model** for data augmentation. Here’s how it was implemented:

##### **3.1 Setup**
1. **API Integration**:
   - Used the OpenRouter API to access the GPT-4o Mini model and Rogue Rose-103b-v0.2 model.
   - Initialized the OpenAI client with the OpenRouter base URL and API key.

2. **Prompt Design**:
   - Designed prompts to generate **5 variations** of each query (Gpt-4o mini):
     ```python
     prompt = f"""Generate 5 different ways to ask this technical support question: {row['user_query']}, and your response be direct meaning give me directly the questions dont add number just sperate them with space"""
     ```
    - Designed prompts to generate **5 variations** of each query (Rogue Rose-103b-v0.2):
        ```python
        prompt = f"""Generate 5 different ways to ask this technical support question: {row['user_query']}
        Important formatting rules:
            - Each question must end with a question mark
            - Questions should be written in a natural, conversational tone
            - Do not include any numbers, bullets, or prefixes
            - Separate questions only with question marks
            - Return the questions as a continuous text without line breaks"""
        ```

##### **3.2 Execution**
1. **GPT-4o Mini model**
    1. **Data Processing**:
        - Looped through the original dataset and generated variations for each query.
        - Split multi-question outputs into individual queries.
    2. **Performance**:
        - Generated **13,798 rows** in **140 minutes**.
        - Achieved a **13.5x increase** in dataset size.

2. **Rogue Rose-103b-v0.2 model**
    1. **Data Processing**:
        - Looped through the original dataset and generated variations for each query.
        - Remobe numbered bullets.
    2. **Performance**:
        - Generated **961 rows** in **39 minutes**.
        - High-quality, diverse queries with different approch than GPT-4o Mini model
##### **3.3 Results**
- **Final Dataset Size**: 13,225 rows.
- **Quality**: High-quality, diverse queries ready for training the chatbot.
- **Diversity**: Generated queries covered a wide range of phrasings and scenarios.

---

#### **4. Challenges and Solutions**
| **Technique** | **Challenges** | **Solutions** |
|------------------------|-----------------------------------------|--------------------------------------------|
| Paraphrasing | Grammar errors, irrelevant content | Not used in final workflow 
| GPT-2 and T5 | irrelevant outputs, repetitive or nonsensical. | Not used in final workflow  |
| Back-Translation | Did not significantly augment the data, generated slight variations of the original queries. | Combined with other augmented datasets to enhance diversity. |
| GPT-4o Mini model | API rate limits, occasional errors | Error handling, retry logic |
| Rogue Rose model| Numbered Bullets | retry logic |

---
#### **5. Translation Process**
  - **Objective**: Translate the augmented dataset from English to French.
  - **Tools Used**: `deep-translator` library
  - **Results**:
    1. **Version 1**:
        - **Data included**: Original Data + Augmented Data by GPT-4o Mini
        - Translate **13,225 rows** in **167 minutes**.
        - We've got 3 dataset :
            - English Version
            - French Version
            - Combination of two versions
    1. **Version 2**:
        - **Data included**: Version 1 + Augmented Data by Rogue-Rose-103b-v0.2 model 
        - Translate **14,165 rows** in **130 minutes**.
        - We've got 3 dataset :
            - English Version
            - French Version
            - Combination of two versions
    1. **Version 3**:
        - **Data included**: Version 2 + Back Translated Data
        - Translate **14,952 rows** in **124 minutes**.
        - We've got 3 dataset :
            - English Version
            - French Version
            - Combination of two versions

---

#### **6. Dataset Statistics**
| **Technique** | **Rows Generated** | **Time Taken** | **Used in Final Dataset** |
|------------------------|---------------------|----------------|---------------------------|
| Paraphrasing | 8,176 | 225 minutes | No |
| Back-Translation | 1022 | 60 minutes | Yes |
| GPT-4o Mini | 13,798 | 140 minutes | Yes |
| Rogue Rose | 961 | 39 minutes | Yes |

---

#### **7. Final Data Workflow**
| **Version** | **Data Included** | **Rows** |
|------------------------|---------------------|----------------|
| 1 | Original + GPT-4o mini Data Generated | 13,225 | 
| 2 | Original + GPT-4o mini Data Generated + Rogue Rose Data Generated | 14,165 | 
| 3 | Original + GPT-4o mini Data Generated + Rogue Rose Data Generated + Back Translated Data | 14952 |

You will find all of them here : [Dataset](/Translation/data)

---

#### **8. Future Work**
- **Fine-Tuning**: Fine-tune the chatbot model on the `3 versions augmented dataset` for better performance.
- **Evaluation**: Evaluate the chatbot’s performance on real-world employee queries.
- **Scaling**: Explore additional techniques (e.g., crowdsourcing) to further increase dataset size.

---

## License

This project is licensed under the **MIT License** – see the [LICENSE](./LICENSE) file for details.
