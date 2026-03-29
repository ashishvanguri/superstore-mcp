# superstore-mcp
An MCP (Model Context Protocol) server is a lightweight bridge that lets an AI model (like me) securely interact with your local data or tools. In your case, it translates an AI's intent into localized Python execution and SQL database queries.

# Superstore BI Agent (MCP Server)

A Model Context Protocol (MCP) server that empowers AI models to act as Business Intelligence agents. This server connects to a SQLite database containing the classic "Superstore" e-commerce dataset and allows an AI to run SQL queries and generate visual charts.

---

## 🚀 Features

* **Schema Discovery:** Allows the AI to inspect table columns so it doesn't have to guess column names.
* **Raw Data Analytics:** Executes full SQL read-only queries to answer complex business questions.
* **Data Visualization:** Generates bar charts automatically and returns them as native PNG images to the AI interface.

---

## 🛠️ Prerequisites

Make sure you have Python 3.10 or higher installed on your machine. You will also need the Superstore SQLite database file named `ecommerce_bi.db` placed in the same directory as the script.

---

Claude Configuration : 


{
  "mcpServers": {
    "superstore-bi": {
      "command": "python",
      "args": [
        "/path/to/your/folder/your_script_name.py"
      ]
    }
  }
}

   




