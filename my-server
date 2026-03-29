import os
import io
import sqlite3
import logging
import base64
import pandas as pd
import matplotlib.pyplot as plt

# Correct imports for the latest MCP SDK
from mcp.server.fastmcp import FastMCP
from mcp.types import ImageContent, TextContent

# 1. INITIALIZE APP
# Removed 'stateless_http=True' because local clients use standard I/O (stdio)
mcp = FastMCP("superstore-bi-agent")

# 2. PATH HANDLING
# This ensures the script looks for the DB in the same folder it is running from
CURRENT_DIR = os.path.dirname(os.path.abspath(__file__))
DB_PATH = os.path.join(CURRENT_DIR, "ecommerce_bi.db")

# Optional: Mute the noisy matplotlib font manager logs
logging.getLogger('matplotlib').setLevel(logging.WARNING)

# --- TOOLS ---

@mcp.tool()
def get_database_info() -> str:
    """Returns the schema of the superstore_dataset table."""
    print("Tool 'get_database_info' was invoked")
    try:
        if not os.path.exists(DB_PATH):
            return f"Error: Database file not found at {DB_PATH}"

        with sqlite3.connect(f"file:{DB_PATH}?mode=ro", uri=True) as conn:
            df = pd.read_sql_query("PRAGMA table_info(superstore_dataset);", conn)
            if df.empty:
                return "Table 'superstore_dataset' not found in database."
            cols = df[['name', 'type']].to_string(index=False)
            return f"Table: superstore_dataset\nColumns:\n{cols}"
    except Exception as e:
        return f"Error reading schema: {str(e)}"

@mcp.tool()
def run_analytics_query(sql_query: str) -> str:
    """
    Execute a SQL query on the 'superstore_dataset' table.
    Example: SELECT Category, SUM(Sales) FROM superstore_dataset GROUP BY Category
    """
    print(f"Tool 'run_analytics_query' was invoked with: {sql_query}")
    try:
        # Using read-only mode for safety in shared environments
        with sqlite3.connect(f"file:{DB_PATH}?mode=ro", uri=True) as conn:
            df = pd.read_sql_query(sql_query, conn)
            if df.empty:
                return "Query executed successfully, but returned no data."
            return df.to_string(index=False)
    except Exception as e:
        return f"Database Error: {str(e)}"

@mcp.tool()
def visualize_data(sql_query: str, title: str, x_label: str, y_label: str) -> list:
    """
    Executes a SQL query and returns a PNG Bar Chart.
    Query must return two columns: the first for labels, the second for values.
    """
    print(f"Tool 'visualize_data' was invoked with title: {title}")
    try:
        with sqlite3.connect(f"file:{DB_PATH}?mode=ro", uri=True) as conn:
            df = pd.read_sql_query(sql_query, conn)
            if df.empty or df.shape[1] < 2:
                return [TextContent(type="text", text="Error: Query returned insufficient data columns.")]

            # Build the visualization
            plt.figure(figsize=(10, 6))
            plt.bar(df.iloc[:, 0].astype(str), df.iloc[:, 1], color='#3498db')
            plt.title(title)
            plt.xlabel(x_label)
            plt.ylabel(y_label)
            plt.xticks(rotation=45)
            plt.tight_layout()

            # Save to an in-memory byte buffer
            buf = io.BytesIO()
            plt.savefig(buf, format="png")
            buf.seek(0)
            
            # Convert bytes to base64 string for the content block
            img_b64 = base64.b64encode(buf.read()).decode("utf-8")
            plt.close() # Prevent memory leaks

            # Return a list containing an ImageContent block
            return [ImageContent(type="image", data=img_b64, mimeType="image/png")]

    except Exception as e:
        return [TextContent(type="text", text=f"Visualization Error: {str(e)}")]

if __name__ == "__main__":
    # Running locally defaults to the 'stdio' transport
    mcp.run()
