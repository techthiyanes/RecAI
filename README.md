---
title: ChatBot
emoji: 🏃
colorFrom: gray
colorTo: gray
sdk: gradio
sdk_version: 3.29.0
app_file: app.py
pinned: false
---

# Recommender AI Agent: Integrating Large Language Models for Interactive Recommendations
This is the Repo for InteRecAgent, a conversational recommendation agent, which applies Large Language Model(LLM) in Conversational Recommender System(CRS). 


## Table of Contents

  - [Table of Contents](#table-of-contents)
  - [InteRecAgent Framework](#interecagent-framework)
  - [Usage](#usage)
  - [License](#license)
  - [Citation](#citation)
  - [Acknowledge](#acknowledge)

## InteRecAgent Framework
<p align="center">
  <img src="./assets/framework.png" alt="InteRecAgnet Framework" width="100%">
  <br>
  <b>Figure 1</b>: InteRecAgent Framework
</p>



InteRecAgent consists of 4 necessary components:

1. LLM: A large language model, which serves as conversational agent.
 
2. Item profile table: A table containing item informations, whose columns consists of id, title, tag, description, price, release_date, visited_num, et al.


3. Query module: A SQL module to query item information in the item profile table.
    - Input: SQL command
    - Output: information queried with SQL


4. Retrieval module: The module aims to retrieve item candidates from the all item corups according to user's intention(requirements). Note that the module does not function in deal with user's personal profile, like user history, user age, et al. Instead, it focuses on what user wants, like "give me some sports games", "I want some popular games". The module should consist of at least two kinds of retrieval tools:
    - SQL tool: The tools is used to deal with complex search condition, which is related to item information. For example, "I want some popular sports games". Then the tool would use SQL command to search in the item profile table.
        - Input: SQL command
        - Output: execution log
    
    - Item similarity tool: The tools aims to retrieve items according to item similarity. Sometimes, user's intention is not clear enough to organized as SQL command, for example, "I want some games similar with Call of Duty", where the requirements is expressed implicitly through item similarity instead of explicit item features. 
        - Input: item name list as string. 
        - Output: execution log


5. Ranking module: Refine the rank of item candidates according to schema (popularity, similarity, preference). User prefernece comprises `prefer` and `unwanted`. The module could be a traditional recommender model, which inputs user and item features and outputs relevant score.
    - Input: ranking schema, user's profile (prefer, unwanted)
    - Output: execution log


## Usage

1. Environments

    
    First, install other packages listed in `requirements.txt`

       ```bash
       cd LLM4CRS
       conda create -n llm4crs python==3.9
       conda activate llm4crs
       pip install -r requirements.txt
       ```

2. Copy resource

   Copy all files in "InteRecAgent/{domain}" in [OneDrive](https://1drv.ms/f/s!Asn0lWVfky4FpF3saprFClmobx8g?e=lgeT0M) / [RecDrive](https://rec.ustc.edu.cn/share/baa4d930-48e1-11ee-b20c-3fee0ba82bbd) to  your local "./LLM4CRS/resources/{domain}".
   If you cannot access those links, please contact <a href="mailto:jialia@microsoft.com">jialia@microsoft.com</a> or <a href="mailto:xuhuangcs@mail.ustc.edu.cn">xuhuangcs@mail.ustc.edu.cn</a>.

3. Run

    - Personal OpenAI API key:

        ```bash
        cd LLM4CRS
        DOMAIN="game" OPENAI_API_KEY="xxxx" python app.py
        ```

    - Azure OpenAI API key:

        ```bash
        cd LLM4CRS
        DOMAIN="game" OPENAI_API_KEY="xxx" OPENAI_API_BASE="xxx" OPENAI_API_VERSION="xxx" OPENAI_API_TYPE="xxx" python app.py
        ```
    
    Note `DOMAIN` represents the item domain, supported `game`, `movie` and `beauty_product` now.

    We support two types of OpenAI API: [Chat](https://platform.openai.com/docs/api-reference/chat) and [Completions](https://platform.openai.com/docs/api-reference/completions). Here are commands for running RecBot with GPT-3.5-turbo and text-davinci-003.

    ```bash
    cd LLMCRS

    # Note that the engine should be your deployment id
    # completion type: text-davinci-003
    OPENAI_API_KEY="xxx" [OPENAI_API_BASE="xxx" OPENAI_API_VERSION="xxx" OPENAI_API_TYPE="xxx"] python app.py --engine text-davinci-003 --bot_type completion

    # chat type: gpt-3.5-turbo, gpt-4 (Recommended)
    OPENAI_API_KEY="xxx" [OPENAI_API_BASE="xxx" OPENAI_API_VERSION="xxx" OPENAI_API_TYPE="xxx"] python app.py --engine gpt-3.5-turbo/gpt-4 --bot_type chat
    ```

    We also provide a shell script `run.sh`, where commonly used arguments are given. You could directly set the API related information in `run.sh`, or create a new shell script `oai.sh` that would be loaded in `run.sh`. GPT-4 API is highly recommended for the InteRecAgent since it has remarkable instruction-following capability.


4. Features    

    There are several optional features to enhance the agent.

    1. History Shortening

        - `enable_shorten`: if true, enable shortening chat history by LLM; else use all chat history

    2. Demonstrations Selector
        
        - `demo_mode`: mode to choose demonstration. Optional values: [`zero`, `fixed`, `dynamic`]. If `zero`, no demonstration would be used (zero-shot). If `fixed`, the first `demo_nums` examples in `demo_dir_or_file` would be used. If `dynamic`, most `demo_nums` related examples would be selected for in-context learning. 
        - `demo_dir_or_file`: the directory or file path of demostrations. If a directory path is given, all `.jsonl` file in the folder would be loaded. If `demo_mode` is `zero`, the argument is invalid. Default None.
        - `demo_nums`: number of demonstrations used in prompt for in-context learning. The argument is invalid when `demo_dir` is not given. Default 3. 

    3. Reflection

        - `enable_reflection`: if true, enable reflection for better plan making
        - `reflection_limits`: maximum times of reflection

        Note that reflection happens after the plan is finished. That means the conversational agent would generate an answer first and then do reflection.

    4. Plan First

        - `plan_first`: if true, the agent would make tool using plan first. There would only a tool executor for LLM to call, where the plan is input. Default true.

        Additionally, we have implemented a version without using the black-box API calling in langchain.
        To enable it, use the following arguments.

        - `langchain`: if true, use langchain in plan-first strategy. Otherwise, the API calls would be made by directly using openai. Default false.


## License
InteRecAgent uses [MIT](./LICENSE) license. All data and code in this project can only be used for academic purposes.


## Citation
Please cite the following paper as the reference if you use our code or data[![Paper](https://img.shields.io/badge/arxiv-PDF-red)](https://arxiv.org/abs/2308.16505).

```
@misc{huang2023recommender,
      title={Recommender AI Agent: Integrating Large Language Models for Interactive Recommendations}, 
      author={Xu Huang and Jianxun Lian and Yuxuan Lei and Jing Yao and Defu Lian and Xing Xie},
      year={2023},
      eprint={2308.16505},
      archivePrefix={arXiv},
      primaryClass={cs.IR}
}
```


## Acknowledge

Thanks to the open source codes of the following projects:

[VisualChatGPT](https://github.com/microsoft/TaskMatrix/blob/main/visual_chatgpt.py) &#8194;
[LangChain](https://github.com/langchain-ai/langchain) &#8194;
[guidance](https://github.com/microsoft/guidance) &#8194;

