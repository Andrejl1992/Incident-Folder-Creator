## 📂 Phase 1: Incident Folder Creator (Python Code)

```python
import os
import datetime
import json

# -----------------------------
# PHASE 1 (Final Clean Version): Incident Folder Creator
# -----------------------------
# What this script does:
# 1. Lets the user choose an incident type (you can type spaces, e.g. "Domestic Violence")
# 2. Assigns a letter identifier for the category
# 3. Creates a folder named by: Date + Category + Identifier + Unique Case Number
# 4. Adds two subfolders inside: "reports" and "evidence"
# 5. Stores case number progress in a JSON file (so numbers keep increasing each run)
# 6. Creates an "incident_summary.txt" with case details
# 7. Adds placeholder for "Assigned Officers" (to be filled in later in Phase 2)
# -----------------------------

# Dictionary mapping incident categories to identifiers
incident_categories = {
    "homicide": "A",
    "assault": "B",
    "theft": "C",
    "domestic_violence": "D",
    "drug_offenses": "E",
    "traffic_violations": "F"
}

# File that stores the last used case number (so numbers are continuous)
case_file = "case_counter.json"

# Load the current case counter, or start at 0 if file doesn't exist
def load_case_counter():
    if os.path.exists(case_file):
        with open(case_file, "r") as f:
            return json.load(f)
    else:
        return {"last_case_number": 0}

# Save the updated case counter back into the JSON file
def save_case_counter(counter):
    with open(case_file, "w") as f:
        json.dump(counter, f)

# Main function: create an incident folder
def create_incident_folder(incident_type):
    # Normalize input: replace spaces with underscores, lowercase everything
    incident_type = incident_type.replace(" ", "_").lower()
    
    # Check if the incident type is valid
    if incident_type not in incident_categories:
        print("Invalid incident type. Please choose from:", list(incident_categories.keys()))
        return
    
    # Load and update case counter
    counter = load_case_counter()
    counter["last_case_number"] += 1
    case_number = counter["last_case_number"]
    save_case_counter(counter)  # Save immediately so the number persists
    
    # Today's date (YYYY-MM-DD)
    today = datetime.date.today().strftime("%Y-%m-%d")
    
    # Get identifier letter for the chosen category
    identifier = incident_categories[incident_type]
    
    # Build folder name: Date_Category_Identifier_CaseNumber
    folder_name = f"{today}_{incident_type}_{identifier}_{case_number:04d}"
    
    # Create main incident folder
    os.makedirs(folder_name, exist_ok=True)
    
    # Create subfolders: "reports" and "evidence"
    os.makedirs(os.path.join(folder_name, "reports"), exist_ok=True)
    os.makedirs(os.path.join(folder_name, "evidence"), exist_ok=True)
    
    # Create an incident summary text file with case details
    summary_path = os.path.join(folder_name, "incident_summary.txt")
    with open(summary_path, "w") as f:
        f.write("=== INCIDENT SUMMARY ===\n")
        f.write(f"Case Number : {case_number:04d}\n")
        f.write(f"Date        : {today}\n")
        f.write(f"Category    : {incident_type}\n")
        f.write(f"Identifier  : {identifier}\n")
        f.write("------------------------\n")
        f.write("Reports Folder : ./reports\n")
        f.write("Evidence Folder: ./evidence\n")
        f.write("------------------------\n")
        f.write("Assigned Officers: \n")  # Placeholder for Phase 2
    
    # Confirmation message
    print(f"Folder created: {folder_name}")
    print("   Subfolders: /reports, /evidence")
    print("   Summary file: incident_summary.txt (with officer placeholder)")

# -----------------------------
# Run the program
# -----------------------------
if __name__ == "__main__":
    print("Available categories:", list(incident_categories.keys()))
    incident = input("Enter incident type: ").strip()
    create_incident_folder(incident)
