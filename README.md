# HC63 Hostel Operations Automation Pipeline

<div align="center">

![Python](https://img.shields.io/badge/Python-Automation-blue?style=for-the-badge&logo=python)
![Selenium](https://img.shields.io/badge/Selenium-Browser%20Automation-green?style=for-the-badge&logo=selenium)
![Excel](https://img.shields.io/badge/Excel-openpyxl-darkgreen?style=for-the-badge)
![WhatsApp](https://img.shields.io/badge/WhatsApp-Web%20Automation-25D366?style=for-the-badge&logo=whatsapp)
![Status](https://img.shields.io/badge/Status-Used%20Internally-success?style=for-the-badge)

</div>

A Python automation pipeline built to streamline daily hostel operations at **Castilho Hostel & Suites 63**.

This project was developed to solve real operational problems in a live hospitality environment, automating repetitive workflows related to reservations, breakfast lists, city tax processing, door code generation, Word document creation, VCF contact generation, and WhatsApp guest communication.

The system is currently used internally and was designed with a practical goal: reduce manual workload, avoid human errors, and make daily operations faster and more reliable.

---

## 📌 Project Context

Hostel operations involve several repetitive daily tasks:

- Exporting reservation data from the PMS
- Preparing breakfast lists
- Preparing city tax lists
- Creating access codes for guests
- Filling printed code sheets
- Registering WhatsApp contacts
- Sending remote check-in messages
- Cleaning generated files after execution

Before automation, these tasks required a significant amount of concentrated manual work and were especially error-prone for people with little computer experience.

This pipeline automates the full process from raw reservation exports to printed operational documents and guest communication.

---

## 🚀 Impact

The automation reduced a workflow that previously took approximately:

- **1h30 of concentrated work for me**
- **up to 3h for a less computer-experienced person**

to approximately:

- **8 minutes of automated execution**

while improving reliability and reducing manual errors.

---

## 🧠 Why This Project Matters

This was not a school project or tutorial.

It was built because there was a real operational need inside a working business. The project required understanding the hostel workflow, identifying bottlenecks, designing a reliable automation process, and improving it incrementally as real-world issues appeared.

The main focus was not overengineering, but building something that works consistently in daily usage.

---

## 🏗 General Architecture

The pipeline is controlled by a central orchestrator:

```text
manager.py
```

`manager.py` is responsible for coordinating the full workflow, including:

- creating the daily log file if it does not exist
- checking required environment credentials
- logging into external platforms
- exporting reservation files
- converting and duplicating Excel files
- launching each automation stage
- printing generated documents
- sending WhatsApp messages
- cleaning temporary files safely

The project follows a simple principle:

> The Excel files are the source of truth.

---

## 🔐 Credentials and Safety

Credentials are stored in the computer environment variables instead of being hardcoded in the source code.

Before starting the automation, the system verifies that the required credentials exist. This avoids running an incomplete process and helps prevent crashes caused by missing configuration.

---

## 🔄 Full Automation Flow

### 1. PMS Login and Reservation Export

The automation logs into **Ynnov**, the PMS used to manage reservations.

From there, it exports two `.xls` files:

1. a file containing all guests currently staying in the hostel
2. a file containing all guests checking in the next day

These files are then converted to `.xlsx` and duplicated depending on the operational task they will support.

---

### 2. Breakfast List Generation

From the reservation export, the automation creates a breakfast list containing:

- guest names
- number of adults
- number of children
- room information
- total number of people

The script also parses and cleans invalid entries, removing guests who should not appear in the breakfast list for several operational reasons.

After the list is generated, it is printed automatically.

```md
<!-- IMAGE PLACEHOLDER -->
<!-- Breakfast list printed -->
![Breakfast List](images/breakfast-list.png)
```

---

### 3. City Tax List Generation

The tax list is generated from reservation data and contains:

- guest names
- dates
- tax values to be paid
- Booking reservation ID
- property associated with the reservation

The final result is prepared in a format suitable for operational usage and tracking.

```md
<!-- IMAGE PLACEHOLDER -->
<!-- Tax list on Google Sheets -->
![Tax List](images/tax-list.png)
```

---

## 🔑 Door Code Automation

The heart of the pipeline is the access code automation.

Starting from a copied `.xlsx` file, the system parses the reservation data to extract and normalize:

- guest first name
- number of nights
- room name in a presentable format
- reservation grouping information

The automation then groups guests by number of nights and creates a shared **street door code** for each group.

After that, it processes each reservation individually and creates a specific **room code** for each guest, with:

- the correct number of nights
- the correct room
- the correct guest assignment

This is one of the most important parts of the system because creating a single guest code manually requires around **50 clicks and/or key presses**.

Each code creation is wrapped in error handling so that if one code fails, the automation logs the problem and continues instead of crashing the entire pipeline.

---

## 📄 Code List Generation

After the access codes are created, the automation generates a final `code.xlsx` file with the following structure:

| Column | Data |
|---|---|
| A | Guest Name |
| B | Room |
| C | Number of Nights |
| D | Room Code |
| E | Street Code |

This information is then inserted into a pre-existing Word document template.

The automation fills the table in the correct format and ensures that the final document is ready to be printed and used by the team.

If the table is completed successfully, the code list is printed automatically.

```md
<!-- IMAGE PLACEHOLDER -->
<!-- Empty Word table before automation -->
![Empty Code Table](images/code-table-empty.png)

<!-- IMAGE PLACEHOLDER -->
<!-- Completed Word table with generated codes -->
![Completed Code Table](images/code-table-completed.png)
```

---

## 📱 WhatsApp and VCF Contact Automation

The pipeline also automates part of the remote check-in process for a separate property.

From another copy of the reservation file, the automation extracts the guests associated with the remote property and formats:

- names
- phone numbers
- room information

It then creates a `.vcf` contact file containing all required guest contacts.

The file is sent to my own WhatsApp, opened on the phone, and imported directly into the contacts list.

This allows multiple guest contacts to be created almost instantly instead of manually registering each one.

After that, the automation uses WhatsApp Web links to send the first remote check-in message to each guest.

To reduce the risk of being blocked or flagged by WhatsApp, messages are sent with a randomized delay between **8 and 10 seconds**.

```md
<!-- IMAGE PLACEHOLDER -->
<!-- WhatsApp messages sent -->
![WhatsApp Messages](images/whatsapp-messages.png)
```

---

## 🧹 Cleanup and Debugging Strategy

At the end of the execution, the automation deletes temporary files that are no longer needed.

However, it intentionally keeps some generated files when useful, especially the code list.

If an error occurs in a specific section, the corresponding file is not deleted. This allows the valid part of the generated data to still be used manually and also helps with debugging.

The goal is:

> Avoid total failure whenever partial recovery is possible.

This approach was important because the project is used in a real operational environment where the work still needs to be completed even if one part of the automation fails.

---

## 🛠 Technologies Used

- Python
- Selenium
- openpyxl
- WhatsApp Web automation
- VCF contact generation
- Excel file processing
- Word document automation
- Environment variables
- Logging
- Browser automation
- File cleanup utilities

---

## 🧩 Main Technical Challenges

### Selenium Reliability

Several parts of the pipeline depend on browser automation. This required handling:

- dynamic pages
- loading delays
- unreliable selectors
- login states
- external service behavior
- explicit waits
- browser session stability

The project prioritizes stable selectors and explicit waits instead of relying on arbitrary sleeps.

---

### Excel Parsing and Data Cleaning

Reservation exports contain data that needs to be cleaned, filtered, converted, and reorganized.

The automation handles:

- `.xls` to `.xlsx` conversion
- duplicated files for different workflows
- row deletion based on keywords
- stale row issues after deletion
- guest filtering
- reservation grouping
- formatted operational outputs

---

### Error Handling and Recovery

The automation is designed to continue whenever possible.

Instead of crashing immediately, it uses logging and localized error handling so that one failed guest code or one failed section does not necessarily destroy the whole workflow.

This was especially important for access code generation, where each guest is processed individually.

---

### Real-World Workflow Design

A major challenge was not only writing code, but understanding the actual hostel workflow well enough to automate it correctly.

The system had to match the way the team works, including:

- printed documents
- existing templates
- WhatsApp habits
- PMS exports
- access code systems
- remote check-in workflow
- manual fallback needs

---

## 📚 What I Learned

This project strengthened my understanding of:

- automation design
- Python scripting
- browser automation with Selenium
- Excel processing with openpyxl
- file generation and cleanup
- operational reliability
- debugging real-world systems
- error handling and recovery
- workflow analysis
- incremental software improvement

Most importantly, it showed me how software can directly solve operational problems and create measurable value in a real environment.

---

## 🔒 Repository Note

This repository does not contain the full private production source code.

The project is currently used internally in a real business environment, and the full implementation includes operational details, credentials, workflows, and integrations that are not appropriate to publish publicly.

This repository exists as a technical overview and portfolio presentation of the project architecture, workflow, and impact.

---

## 👤 Author

- João Muñoz
