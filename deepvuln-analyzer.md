# 📄 Dokumentasi: CVE Embedding Search & DeepSeek Analyzer

## 📊 Arsitektur / Diagram Alur
![image](https://github.com/user-attachments/assets/c6ecec4e-37a0-4073-915b-ebe0aa81aa0c)

### 🖼️ Screenshot Output
![image](https://github.com/user-attachments/assets/6badaa84-19ec-448e-850b-cc91138e3c4d)


## 📌 Pendahuluan

Proyek ini bertujuan untuk membangun pipeline pencarian dan analisis kerentanan (CVE) dengan pendekatan hybrid. Pendekatan ini menggabungkan pencarian embedding menggunakan Sentence-BERT dan Faiss, dengan analisis lanjutan berbasis model bahasa besar (LLM) DeepSeek.

## ⚙️ Teknologi yang Digunakan

- Python 3
- Sentence-BERT (`sentence-transformers`)
- FAISS untuk indexing dan similarity search
- DeepSeekR1_Qwen_32B (via API lokal)
- AIOHTTP dan Requests
- JSON dan Regex

## 🔁 Alur Kerja

1. **Load Data JSON CVE**  
   Memuat data CVE dari file `cve_data.json`. Data harus memiliki struktur minimal:
   ```json
   {
     "id": "CVE-2023-XXXX",
     "output": "deskripsi kerentanan ..."
   }
   ```

2. **Embedding dan Indexing**  
   - Menggunakan model `all-MiniLM-L6-v2` dari SentenceTransformer untuk membuat embedding dari kolom `output`.
   - Embedding dinormalisasi dan disimpan ke dalam index Faiss (`cve_index.faiss`).

3. **Search Engine (CVE Search)**  
   - Hybrid search:
     - Pertama mencari berdasarkan keyword.
     - Jika gagal, menggunakan embedding similarity search (Faiss).
   - Threshold disesuaikan otomatis berdasarkan jumlah kata dari query.

4. **Analisis LLM DeepSeek**
   - Setelah hasil pencarian diperoleh, dikirimkan ke model LLM lokal DeepSeek.
   - Prompt disusun dengan struktur analisis teknikal CVE.
   - Sistem validasi otomatis mengecek apakah hasil sudah sesuai struktur.

5. **Self-Refinement**
   - Jika hasil tidak lengkap atau terlalu pendek, sistem memberikan feedback otomatis dan mengulang prompt dengan instruksi tambahan.
   - Refinement maksimal dilakukan hingga 3 kali atau sampai hasil valid.

6. **Log dan Output**
   - Informasi lengkap CVE (dari NVD API) dan hasil analisis ditampilkan/log secara terstruktur.

## 🧠 Status Saat Ini

✅ Implementasi dasar sudah lengkap, termasuk:
- Pencarian keyword & embedding
- Faiss indexing & similarity search
- Interaksi dengan DeepSeek API
- Validasi otomatis dan self-refinement

❌ Belum selesai:
- Dokumentasi lengkap untuk deployment
- Evaluasi performa pencarian (recall/precision)
- UI untuk interaksi
- Logging lebih detail untuk hasil refinement

## 🚧 Catatan Penting

- Model DeepSeek diasumsikan sudah berjalan di server lokal dengan path dan model sesuai konfigurasi.
- Validasi hasil masih berbasis regex sederhana, belum pakai scoring NLP.
- Belum ada caching untuk response atau indexing.

## ✅ Kesimpulan

Proyek ini berhasil membuat pipeline yang solid untuk:
- Pencarian berbasis similarity dan keyword
- Integrasi LLM untuk analisis lanjutan
- Validasi dan perbaikan otomatis hasil model

Namun proyek ini **masih dalam tahap pengembangan** dan belum 100% selesai.

---

> Jika Anda ingin menggunakan proyek ini, pastikan Anda sudah memiliki model DeepSeek dan file `cve_data.json` yang valid.


## ✅ JSON EXAMPLE
```
[
    {
        "id": "CVE-2025-21697",
        "output": "The Linux kernel is vulnerable due to improper handling of job pointers. Specifically, after a job completes, the job pointer must be explicitly set to NULL to prevent warnings and potential crashes when unloading the driver. This vulnerability can lead to unintended behavior or memory corruption. The issue arises in the kernel's handling of job management, where a stale pointer may cause dereferencing of invalid memory. This issue affects kernel versions prior to the fix in the latest release, which addresses the pointer management. System administrators are advised to update their kernels to the latest stable version to mitigate potential risks of exploitation."
    },
    {
        "id": "CVE-2025-27760",
        "output": "This CVE was rejected and is not assigned for use. After review, it was determined that the issue it referred to did not exist or did not meet the criteria for inclusion in the CVE database. As such, there is no further action or mitigation required for this CVE."
    },
    {
        "id": "CVE-2025-0652",
        "output": "A critical vulnerability exists in GitLab EE/CE versions 16.9 before 17.7.7, 17.8 before 17.8.5, and 17.9 before 17.9.2. Unauthorized users can exploit this vulnerability to gain access to confidential information. The issue arises due to improper access controls that fail to sufficiently restrict certain sensitive data, allowing unauthorized users to view or manipulate it. This can result in data leakage or unauthorized access to sensitive project information. GitLab has released fixes for affected versions, and users are strongly recommended to upgrade to the latest patched versions to prevent exploitation of this vulnerability."
    },
    {
        "id": "CVE-2025-0001",
        "output": "Abacus ERP Software is affected by a vulnerability that allows authenticated users to access and read arbitrary files on the system. This issue arises in versions prior to 2024.210.16036, 2023.205.15833, and 2022.105.15542. The root cause is insufficient access control mechanisms that fail to properly restrict file access based on user permissions. As a result, authenticated users can bypass access restrictions and access sensitive files that they should not have access to. This could lead to information disclosure or the potential for further attacks. It is advised to update to the latest available version of the software, which addresses these access control issues and mitigates the risk of exploitation."
    },
    {
        "id": "CVE-2025-25352",
        "output": "PHPGurukul Land Record System v1.0 is vulnerable to SQL Injection in the /admin/aboutus.php file. This flaw allows remote attackers to inject arbitrary SQL queries into the application, potentially enabling them to execute arbitrary code, manipulate database contents, or access sensitive data. The vulnerability arises because user input from the 'pagetitle' POST parameter is not properly sanitized, allowing attackers to bypass application controls and exploit the database directly. To mitigate the risk of exploitation, it is recommended to validate and sanitize all user input, especially when handling database queries, to prevent the injection of malicious SQL code."
    },
    {
        "id": "CVE-2025-25355",
        "output": "PHPGurukul Land Record System v1.0 contains another SQL Injection vulnerability, this time in the /admin/bwdates-reports-details.php file. Similar to CVE-2025-25352, the vulnerability allows remote attackers to execute arbitrary SQL queries via the 'fromdate' POST parameter. This flaw can lead to unauthorized access to or manipulation of the underlying database. Attackers can leverage this vulnerability to gain access to sensitive records, modify data, or compromise the system further. It is crucial to ensure that all user inputs are properly validated and sanitized to prevent SQL injection attacks and secure the system from unauthorized access."
    },
    {
        "id": "CVE-2025-0705",
        "output": "A vulnerability has been found in JoeyBling bootplus up to 247d5f6c209be1a5cf10cd0fa18e1d8cc63cf55d and classified as problematic. Affected by this vulnerability is the function qrCode of the file src/main/java/io/github/controller/QrCodeController.java. The manipulation of the argument text leads to open redirect. The attack can be launched remotely. The exploit has been disclosed to the public and may be used. This product takes the approach of rolling releases to provide continuous delivery. Therefore, version details for affected and updated releases are not available."
    },
    {
        "id": "CVE-2025-26309",
        "output": "A memory leak has been identified in the parseSWF_DEFINESCENEANDFRAMEDATA function in util/parser.c of libming v0.4.8, which allows attackers to cause a denial of service via a crafted SWF file."
    },
    {
        "id": "CVE-2025-23651",
        "output": "Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting') vulnerability in NotFound Scroll Top allows Reflected XSS. This issue affects Scroll Top: from n/a through 1.3.3."
    },
    {
        "id": "CVE-2025-23817",
        "output": "Cross-Site Request Forgery (CSRF) vulnerability in Mahadir Ahmad MHR-Custom-Anti-Copy allows Stored XSS. This issue affects MHR-Custom-Anti-Copy: from n/a through 2.0."
    },
    {
        "id": "CVE-2025-26569",
        "output": "Cross-Site Request Forgery (CSRF) vulnerability in callmeforsox Post Thumbs allows Stored XSS. This issue affects Post Thumbs: from n/a through 1.5."
    },
    {
        "id": "CVE-2025-25352",
        "output": "A SQL Injection vulnerability was found in /admin/aboutus.php in PHPGurukul Land Record System v1.0, which allows remote attackers to execute arbitrary code via the pagetitle POST request parameter."
    },
    {
        "id": "CVE-2025-25942",
        "output": "An issue in Bento4 v1.6.0-641 allows an attacker to obtain sensitive information via the mp4fragment tool when processing invalid files. Specifically, memory allocated in SampleArray::SampleArray in Mp4Fragment.cpp is not properly released."
    },
    {
        "id": "CVE-2025-27583",
        "output": "Incorrect access control in the component /rest/staffResource/findAllUsersAcrossOrg of Serosoft Solutions Pvt Ltd Academia Student Information System (SIS) EagleR v1.0.118 allows create and modify user accounts, including an Administrator account."
    },
    {
        "id": "CVE-2025-21180",
        "output": "Heap-based buffer overflow in Windows exFAT File System allows an unauthorized attacker to execute code locally."
    },
    {
        "id": "CVE-2025-27677",
        "output": "Vasion Print (formerly PrinterLogic) before Virtual Appliance Host 22.0.843 Application 20.0.1923 allows Symbolic Links For Unprivileged File Interaction V-2022-002."
    },
    {
        "id": "CVE-2025-1619",
        "output": "The GDPR Cookie Compliance WordPress plugin before 4.15.7 does not sanitise and escape some of its settings, which could allow high privilege users such as admin to perform Stored Cross-Site Scripting attacks even when the unfiltered_html capability is disallowed (for example in multisite setup)."
    },
    {
        "id": "CVE-2025-1300",
        "output": "CodeChecker is an analyzer tooling, defect database and viewer extension for the Clang Static Analyzer and Clang Tidy. The CodeChecker web server contains an open redirect vulnerability due to missing protections against multiple slashes after the product name in the URL. This results in bypassing the protections against CVE-2021-28861, leading to the same open redirect pathway. This issue affects CodeChecker: through 6.24.5."
    },
    {
        "id": "CVE-2025-26603",
        "output": "Vim is a greatly improved version of the good old UNIX editor Vi. Vim allows to redirect screen messages using the ':redir' ex command to register, variables and files. It also allows to show the contents of registers using the ':registers' or ':display' ex command. When redirecting the output of ':display' to a register, Vim will free the register content before storing the new content in the register. Now when redirecting the ':display' command to a register that is being displayed, Vim will free the content while shortly afterwards trying to access it, which leads to a use-after-free. Vim pre 9.1.1115 checks in the ex_display() function, that it does not try to redirect to a register while displaying this register at the same time. However, this check is not complete, and so Vim does not check the '+' and '*' registers (which typically donate the X11/clipboard registers, and when a clipboard connection is not possible will fall back to use register 0 instead. In Patch 9.1.1115 Vim will therefore skip outputting to register zero when trying to redirect to the clipboard registers '*' or '+'. Users are advised to upgrade. There are no known workarounds for this vulnerability."
    },
    {
        "id": "CVE-2025-26626",
        "output": "The GLPI Inventory Plugin handles various types of tasks for GLPI agents for the GLPI asset and IT management software package. Versions prior to 1.5.0 are vulnerable to reflective cross-site scripting, which may lead to executing javascript code. Version 1.5.0 fixes the issue."
    },
    {
        "id": "CVE-2025-26905",
        "output": "Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal') vulnerability in Estatik Estatik allows PHP Local File Inclusion. This issue affects Estatik: from n/a through 4.1.9."
    },
    {
        "id": "CVE-2025-23041",
        "output": "Umbraco.Forms is a web form framework written for the nuget ecosystem. Character limits configured by editors for short and long answer fields are validated only client-side, not server-side. This issue has been patched in versions 8.13.16, 10.5.7, 13.2.2, and 14.1.2. Users are advised to upgrade. There are no known workarounds for this issue."
    },
    {
        "id": "CVE-2025-29310",
        "output": "An issue in onos v2.7.0 allows attackers to trigger a packet deserialization problem when supplying a crafted LLDP packet. This vulnerability allows attackers to execute arbitrary commands or access network information."
    },
    {
        "id": "CVE-2025-25194",
        "output": "Lemmy, a link aggregator and forum for the fediverse, is vulnerable to server-side request forgery via a dependency on activitypub_federation, a framework for ActivityPub federation in Rust. This vulnerability, which is present in versions 0.6.2 and prior of activitypub_federation and versions 0.19.8 and prior of Lemmy, allows a user to bypass any predefined hardcoded URL path or security anti-Localhost mechanism and perform an arbitrary GET request to any Host, Port and URL using a Webfinger Request. As of time of publication, a fix has not been made available."
    },
    {
        "id": "CVE-2025-22629",
        "output": "Missing Authorization vulnerability in iNET iNET Webkit allows Accessing Functionality Not Properly Constrained by ACLs. This issue affects iNET Webkit: from n/a through 1.2.2."
    },
    {
        "id": "CVE-2025-1714",
        "output": "Lack of Rate Limiting in Sign-up workflow in Perforce Gliffy prior to version 4.14.0-7 on Gliffy online allows attacker to enumerate valid user emails and potentially DOS the server."
    },
    {
        "id": "CVE-2025-21331",
        "output": "Windows Installer Elevation of Privilege Vulnerability."
    },
    {
        "id": "CVE-2025-1180",
        "output": "A vulnerability classified as problematic has been found in GNU Binutils 2.43. This affects the function _bfd_elf_write_section_eh_frame of the file bfd/elf-eh-frame.c of the component ld. The manipulation leads to memory corruption. It is possible to initiate the attack remotely. The complexity of an attack is rather high. The exploitability is told to be difficult. The exploit has been disclosed to the public and may be used. It is recommended to apply a patch to fix this issue."
    },
    {
        "id": "CVE-2025-29907",
        "output": "jsPDF is a library to generate PDFs in JavaScript. Prior to 3.0.1, user control of the first argument of the addImage method results in CPU utilization and denial of service. If exploited, an attacker may force the browser into an infinite loop."
    }
]
```

## ✅ CODE
```
import json
import aiohttp
from aiohttp import ClientSession, TCPConnector
import asyncio
import requests
import logging
import numpy as np
from sentence_transformers import SentenceTransformer
import faiss
import re

# logging.basicConfig(level=logging.INFO, format="%(asctime)s - %(levelname)s - %(message)s")


# Fungsi untuk memuat data CVE dari file JSON
def load_cve_data(file_path):
    try:
        with open(file_path, 'r') as f:
            return json.load(f)
    except FileNotFoundError:
        logging.error(f"File {file_path} tidak ditemukan!")
        return []
    except json.JSONDecodeError:
        logging.error(f"File {file_path} bukan JSON yang valid!")
        return []

# Inisialisasi model Sentence-BERT
class CVEModel:
    def __init__(self, model_name='all-MiniLM-L6-v2'):
        self.model = SentenceTransformer(model_name)

    def encode_texts(self, texts):
        embeddings = self.model.encode(texts)
        return embeddings / np.linalg.norm(embeddings, axis=1, keepdims=True)

# Inisialisasi Faiss Index
class FaissIndex:
    def __init__(self, embeddings):
        self.d = embeddings.shape[1]
        self.index = faiss.IndexFlatIP(self.d)
        self.index.add(embeddings)
    
    def search(self, query_embedding, k=1):
        distances, indices = self.index.search(query_embedding, k)
        return distances, indices

    def save(self, filename):
        faiss.write_index(self.index, filename)

# Fungsi untuk menyesuaikan threshold berdasarkan panjang query
def adjust_threshold(query):
    words = query.split()
    filtered_words = [word for word in words if len(word) > 3]
    num_words = len(filtered_words)
    
    if num_words > 2:
        return 0.5  # Untuk query lebih dari 2 kata, threshold-nya lebih rendah
    elif num_words < 2:
        return 0.1  # Jika hanya ada satu kata lebih panjang dari 3 karakter
    elif num_words == 2:
        return 0.3  # Jika hanya ada satu kata lebih panjang dari 3 karakter
    return 0.7  # Default threshold untuk query lainnya

# Fungsi untuk mencari ID terlebih dahulu, baru melakukan pencarian berbasis embedding
class CVESearch:
    def __init__(self, data, model, faiss_index):
        self.data = data
        self.model = model
        self.faiss_index = faiss_index
    
    def search_by_keywords(self, query, threshold=0.7):
        query_lower = query.lower()
        matching_entries = []

        for entry in self.data:
            matching_words = [word for word in query_lower.split() if word in entry['output'].lower()]
            if matching_words:
                logging.debug(f"Matching words found in: {entry['id']}")  # Debug
                output_embedding = self.model.encode_texts([entry['output']])
                query_embedding = self.model.encode_texts([query])
                
                similarity = np.dot(query_embedding, output_embedding.T)[0][0]
                logging.debug(f"Similarity for {entry['id']}: {similarity}")  # Debug
                if similarity >= threshold:
                    matching_entries.append({
                        'ID': entry['id'],
                        'Distance': similarity,
                        'Output': entry['output']
                    })
        return matching_entries

    def search_query(self, query, k=1, threshold=0.7):
        threshold = adjust_threshold(query)
        logging.debug(f"Searching for: {query}") 

        keyword_results = self.search_by_keywords(query, threshold)
        if keyword_results:
            logging.debug(f"Keyword search found: {len(keyword_results)} results") 
            return keyword_results  # If match found, return keyword results

        query_embedding = self.model.encode_texts([query])
        distances, indices = self.faiss_index.search(query_embedding, k)

        results = []
        for i in range(len(indices[0])):  # Only access as many results as found
            idx = indices[0][i]
            if distances[0][i] >= threshold:
                result = {
                    'ID': self.data[idx]['id'],
                    'Distance': distances[0][i],
                    'Output': self.data[idx]['output']
                }
                results.append(result)
        
        return results

# Konfigurasi API
BASE_URL = "{{URL}}"
MODEL_NAME = "{{DeepSeekModel}}"
HEADERS = {
    "Content-Type": "application/json"
}

def chat_with_model(prompt):
    payload = {
        "model": MODEL_NAME,
        "messages": [{"role": "user", "content": prompt}],
        "max_tokens": 5000
    }
    
    response = requests.post(f"{BASE_URL}/chat/completions", json=payload, headers=HEADERS)
    
    if response.status_code == 200:
        return response.json()
    else:
        return {"error": f"Request failed with status code {response.status_code}"}

# Menyiapkan data CVE dan model
data = load_cve_data("cve_data.json") 

# Menyiapkan model dan index
cve_model = CVEModel()
output_texts = [entry['output'] for entry in data]
output_embeddings = cve_model.encode_texts(output_texts)
faiss_index = FaissIndex(output_embeddings)
faiss_index.save("cve_index.faiss")

# Inisialisasi pencarian
cve_search = CVESearch(data, cve_model, faiss_index)

# Contoh query
# query = "critical vulnerability exists in GitLab EE/CE"
# query = "Linux kernel is vulnerable"
query = "server-side request forgery"
# query = "disinilah apa adanya aku tidak tahu juga"
results = cve_search.search_query(query, k=5, threshold=0.5)

# Menampilkan hasil dan memproses analisis dari DeepSeek
for result in results:
    logging.info(f"ID: {result['ID']}")
    logging.info(f"Distance: {result['Distance']}")
    logging.info(f"Output: {result['Output']}\n")


# Membersihkan tag <think> dari response
def clean_deepseek_response(response):
    return re.sub(r"<think>.*?</think>", "", response, flags=re.DOTALL).strip()

# Fetch data CVE (nonaktifkan SSL verification)
async def fetch_cve_data(session, url):
    connector = TCPConnector(ssl=False)
    async with ClientSession(connector=connector) as session:
        async with session.get(url) as response:
            if response.status == 200:
                return await response.json()
            logging.error(f"Failed to fetch data from {url} with status {response.status}")
            return None

# Parsing data CVE dari hasil API NVD
def parse_cve_data(response):
    cve_item = response.get("vulnerabilities", [])[0].get("cve", {})
    return {
        "id": cve_item.get("id", "N/A"),
        "published": cve_item.get("published", "N/A"),
        "last_modified": cve_item.get("lastModified", "N/A"),
        "description": next((d.get("value") for d in cve_item.get("descriptions", []) if d.get("lang") == "en"), "No description available"),
        "cvss_v40": next((m.get("cvssData", {}).get("baseScore") for m in cve_item.get("metrics", {}).get("cvssMetricV40", [])), "N/A"),
        "cvss_v31": next((m.get("cvssData", {}).get("baseScore") for m in cve_item.get("metrics", {}).get("cvssMetricV31", [])), "N/A"),
        "cvss_v2": next((m.get("cvssData", {}).get("baseScore") for m in cve_item.get("metrics", {}).get("cvssMetricV2", [])), "N/A"),
        "references": [ref.get("url") for ref in cve_item.get("references", [])]
    }

# Menampilkan info CVE ke log
def log_cve_info(cve_info):
    logging.info(f"CVE ID: {cve_info['id']}")
    logging.info(f"Published Date: {cve_info['published']}")
    logging.info(f"Last Modified: {cve_info['last_modified']}")
    logging.info(f"Description: {cve_info['description']}")
    logging.info(f"CVSS Score (v4.0): {cve_info['cvss_v40']}")
    logging.info(f"CVSS Score (v3.1): {cve_info['cvss_v31']}")
    logging.info(f"CVSS Score (v2.0): {cve_info['cvss_v2']}")
    logging.info("References:")
    for ref in cve_info["references"]:
        logging.info(f"  - {ref}")

# Menyusun prompt untuk DeepSeek dengan instruksi lebih kuat
def build_prompt(advisories, cve_info, refinement_feedback=None, previous_response=None):
    base = f"""
You are a cybersecurity expert. Analyze the following vulnerability advisories and provide structured responses for each one.  
Ensure your response is detailed, well-structured, and follows the format below.

### CVE ID:
{cve_info['id']}

### Vulnerability Type:
(Select the most relevant type: Buffer Overflow, SQL Injection, Cross-Site Scripting (XSS), Denial of Service (DoS), etc.)

### Vulnerability Description:
(Provide a detailed yet concise technical explanation, including how the vulnerability occurs and its impact.)

### Severity Level:
(Critical, High, Medium, Low — Ensure the level aligns with CVSS scores.)

### Affected Versions:
(Specify affected versions explicitly, based on advisory data.)

### Exploitability:
(Easy, Moderate, Difficult, or Zero-Day — Clearly state the likelihood of exploitation.)

### Affected Components:
(Specify the affected components precisely.)

### Mitigation or Workaround:
(Provide actionable recommendations, including patches, configurations, or temporary mitigations.)

**Advisories:**  
{advisories}

**Additional Information:**  
CVE ID: {cve_info['id']}  
Published Date: {cve_info['published']}  
Last Modified: {cve_info['last_modified']}  
Description: {cve_info['description']}  
CVSS Score (v4.0): {cve_info['cvss_v40']}  
CVSS Score (v3.1): {cve_info['cvss_v31']}  
CVSS Score (v2.0): {cve_info['cvss_v2']}  

**References:**  
"""
    for ref in cve_info["references"]:
        base += f"  - {ref}\n"

    if previous_response:
        base += f"\n\n### Previous Response:\n{previous_response}\n"

    if refinement_feedback:
        base += f"\n\n### Refinement Instructions:\n{refinement_feedback}\n"

    return base

# Validasi hasil DeepSeek
def validate_response(response):
    required_sections = ["Vulnerability Type", "Vulnerability Description", "Severity Level", "Exploitability"]
    
    for section in required_sections:
        if f"### {section}:" not in response:
            return False  # Jika ada bagian yang hilang, hasil dianggap tidak valid
    return True

# Mengecek apakah perubahan cukup signifikan
def is_significant_change(old_response, new_response, threshold=0.1):
    """
    Membandingkan panjang karakter dari respons lama dan baru.
    Jika perubahan kurang dari `threshold`, maka dianggap tidak signifikan.
    """
    old_len, new_len = len(old_response), len(new_response)
    if old_len == 0:
        return True  # Jika respons lama kosong, maka perubahan pasti signifikan

    change_ratio = abs(new_len - old_len) / old_len
    return change_ratio > threshold  # Jika perubahan di atas threshold, maka signifikan


# Proses self-refinement dengan instruksi lebih spesifik
def refine_deepseek_response(original_prompt, deepseek_response):
    issues = []
    
    # Validasi bagian yang hilang atau kurang kuat
    if "Vulnerability Type:" not in deepseek_response:
        issues.append("Missing 'Vulnerability Type'. Provide a clear classification.")
    if "Vulnerability Description:" not in deepseek_response or len(deepseek_response.split("Vulnerability Description:")[1].strip()) < 50:
        issues.append("Vulnerability description is too short. Explain how the vulnerability works and its impact.")
    if "Severity Level:" not in deepseek_response:
        issues.append("Missing 'Severity Level'. Justify the rating based on CVSS scores.")
    if "Exploitability:" not in deepseek_response:
        issues.append("Missing 'Exploitability'. Assess whether it is easy, moderate, difficult, or a zero-day.")

    # Jika ada kekurangan, buat umpan balik
    if issues:
        feedback = "Your previous response is incomplete or lacks detail. Improve the response based on these points:\n"
        feedback += "\n".join(f"- {issue}" for issue in issues)
        refined_prompt = build_prompt(original_prompt, None, refinement_feedback=feedback, previous_response=deepseek_response)
        return refined_prompt, True  # Refinement diperlukan

    return deepseek_response, False  # Tidak perlu refinement

# Proses batch advisories dengan self-refinement
async def process_advisories_in_batches(results, batch_size=2):
    async with aiohttp.ClientSession() as session:
        for i in range(0, len(results), batch_size):
            batch = results[i:i + batch_size]
            advisories = "\n\n".join([f"ID: {res['ID']}\nOutput: {res['Output']}" for res in batch])

            tasks = [fetch_cve_data(session, f"https://services.nvd.nist.gov/rest/json/cves/2.0?cveId={res['ID']}") for res in batch]
            responses = await asyncio.gather(*tasks)

            for response, res in zip(responses, batch):
                if response:
                    cve_info = parse_cve_data(response)
                    log_cve_info(cve_info)

                    prompt = build_prompt(advisories, cve_info)
                    retries = 3  # Maksimal 3 kali refinements
                    previous_response = ""

                    while retries > 0:
                        analysis_result = chat_with_model(prompt)
                        if "choices" in analysis_result:
                            raw_message = analysis_result['choices'][0]['message']['content']
                            cleaned_message = clean_deepseek_response(raw_message)

                            if validate_response(cleaned_message):
                                if retries < 3 and not is_significant_change(previous_response, cleaned_message):
                                    logging.info("Refinement stopped: No significant change detected.")
                                    break  # Refinement dihentikan jika perubahan tidak signifikan

                                logging.info("\nDeepSeek Analysis Result:\n")
                                print(cleaned_message)
                                break  # Hasil sudah valid, keluar dari loop refinement

                            previous_response = cleaned_message
                            prompt, needs_refinement = refine_deepseek_response(prompt, cleaned_message)

                            if not needs_refinement:
                                break  # Tidak perlu refinement lagi

                        retries -= 1

# Eksekusi utama
if results:
    asyncio.run(process_advisories_in_batches(results, batch_size=2))
```
