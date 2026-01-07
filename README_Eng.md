# 30 Days of AI
## The ```#30DaysOfAI``` is a coding challenge designed to help you get started in building AI apps with Streamlit.

## Day1

***1. Import Libraries***
```
import streamlit as st 
```

***2. Connect to Snowflake***
```
# Auto-detect environment and connect
try:
    from snowflake.snowpark.context import get_active_session
    session = get_active_session()
except:
    from snowflake.snowpark import Session
    session = Session.builder.configs(st.secrets["connections"]["snowflake"]).create()
```





## Day2








## Day3









## Day4











## Day5