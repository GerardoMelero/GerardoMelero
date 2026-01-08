<h1>Hi, I'm Gerardo! </h1>
<h2>👨‍💻 Cybersecurity Projects:</h2>

- [Active Directory Home Lab]

<h2> 📄 Certifications</h2>
 [CompTIA Security+ Certified]

Microsoft SC-200 in progress

- [Google Cybersecurity Professional Certificate]


- [Tutorial Active Directory Home Lab] 

<h2> 🤳 Connect with me:</h2>

[<img align="left" alt="gerardomelero | LinkedIn" width="22px" src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/linkedin.svg" />][linkedin]



[linkedin]: https://linkedin.com/in/gerardomelero

**GerardoMelero/GerardoMelero** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Sysmon → Splunk Validation Lab (Sysmon Event ID 1: Process Create)
Overview

In this lab, I verified my telemetry pipeline end-to-end: Sysmon on a Windows endpoint → logs shipped into Splunk → evidence confirmed with a search. To keep it simple and repeatable, I generated one baseline action (whoami /groups) and traced how it appeared in Splunk as a Sysmon Event ID 1 (Process Create) event.

I also converted the raw XML event into JSON and used JSONCrack strictly for visualization so I could clearly understand the event structure—especially the EventData section.

Environment (high-level)

Endpoint: Windows 11 VM (Win11V)

Telemetry: Sysmon (Microsoft-Windows-Sysmon/Operational)

SIEM: Splunk Enterprise

Ingestion format: XML (XmlWinEventLog)

Note: Hostnames, domains, and IPs will vary. My screenshots reflect my lab.

Step 1 — I generated endpoint activity

On my Windows 11 VM, I opened an elevated Command Prompt and ran: whoami /groups

Settings I used while searching:

I enabled Verbose Mode so Splunk would extract as many fields as possible while I’m learning.

I set the time range wide enough to catch the event (example: last 15 minutes).

Screenshot: Splunk results (raw XML event visible)


Step 3 — I identified the event as Sysmon Event ID 1

The event returned in Splunk was Sysmon Event ID 1, which is Process Create. That matters because it captures the execution context I care about for detection work, including:

process image (Image)

command line (CommandLine)

parent process (ParentImage)

user context (User)

integrity level (IntegrityLevel)

hashes (when configured)

Step 4 — I visualized the event structure (XML → JSON → JSONCrack)

Splunk ingested the event as XML, which is totally fine for searching, but it can be harder to read quickly.

To make the structure easier to understand, I converted the event to JSON and loaded it into JSONCrack. This made it easier for me to see the event broken into logical sections (for example: Provider, Execution, Security) and focus on what matters most for detections: EventData.

Important: I did not use JSON for Splunk searching. I only used JSONCrack as a visualization aid.

Screenshot: JSONCrack visualization


Step 5 — I mapped my actions to the telemetry fields

To avoid getting overwhelmed by the number of fields, I tied the telemetry directly back to what I did:

What I did	Sysmon field	Example value
I opened Command Prompt	ParentImage	C:\Windows\System32\cmd.exe
I ran the command	CommandLine	whoami /groups
The process that launched	Image	C:\Windows\System32\whoami.exe
The account it ran under	User	CONDEF\Administrator
The privilege level	IntegrityLevel	High

A couple extra fields I noted:

Hashes: contains file hashes for the executable (useful for validation/hunting).

OriginalFileName: can remain whoami.exe even if the file is renamed (useful for spotting masquerading).

Screenshot setup

For the image links above to work, I placed my screenshots in an images/ folder using these names:

images/01-whoami-groups.png

images/02-splunk-search.png

images/03-jsoncrack-visual.png
