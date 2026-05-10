# VPC Subnet Learning Calculator

> **Disclaimer:** This project is created for **learning and educational purposes only**. It is not affiliated with, endorsed by, or associated with Amazon Web Services (AWS) in any way. All AWS-related terminology, concepts, and naming conventions are used solely for educational reference. Always refer to the [official AWS documentation](https://docs.aws.amazon.com/) for production use.

---

## Overview

The **VPC Subnet Learning Calculator** is a single-file, browser-based tool that helps you understand how AWS VPC subnetting works. It takes your subnet requirements (names, types, and number of IPs needed) and calculates the correct CIDR blocks, usable IP counts, IP ranges, and more, all without installing anything.

The tool is entirely client-side. No data is sent to any server. It works offline once loaded.

**Live Demo:** [https://pavankrishnaer.github.io/vpc-calculator/](https://pavankrishnaer.github.io/vpc-calculator/)

---

## Features

- **Use Case Templates:** Start from a pre-built scenario (Web App, Microservices, Data Pipeline, Startup, and more) or build from scratch
- **Subnet Definition Builder:** Define multiple subnets with custom names, types (public/private), and required IP counts
- **VPC CIDR Advisor:** Get real-time feedback on whether your chosen VPC prefix fits all your subnets with enough headroom
- **Multi-AZ Support:** Distribute subnets across 1 to 4 Availability Zones with per-AZ toggle control
- **Detailed Results Table:** View CIDR block, IP range, total IPs, usable IPs, and reserved IP breakdown for each subnet
- **IP Address Breakdown Popover:** Click any CIDR result to see the full IP list including AWS reserved addresses
- **Visual IP Bar:** A proportional bar chart showing how subnets consume VPC space
- **Architecture Diagram:** An auto-generated visual of your VPC layout including Internet Gateway, NAT Gateway, and AZ columns
- **Quiz Mode:** Test your CIDR knowledge by hiding results and guessing the correct subnet prefix before revealing the answer
- **Share via URL:** Encode your current configuration into the URL so you can bookmark or share it
- **Progress Indicator:** A sticky step bar guides you through the three-step workflow
- **Fully Responsive:** Works on desktop and mobile browsers

---

## How to Use

The calculator follows a three-step workflow.

### Step 0: Choose a Use Case (Optional)

At the top of the page, a grid of use case cards is displayed. Each card represents a common AWS architecture pattern:

| Use Case | Description |
|---|---|
| Web App | Public ALB + private API + private DB |
| Three-Tier | Classic presentation, application, and data tiers |
| ECS / Fargate | Container workloads with ALB and private services |
| Serverless | Lambda functions with minimal subnets |
| Startup (Small) | Simple two-subnet layout for small teams |
| Microservices | Multiple isolated subnets per service |
| Data Pipeline | Glue, EMR, or Redshift workloads with large IP blocks |
| Just Learning | Experimental sandbox with no fixed workload |
| Custom / Scratch | Blank slate where you define everything yourself |

Click a card to see a description and a preview of the subnets it will create. Click **Apply to Step 1** to load that configuration automatically. You can then modify it freely.

---

### Step 1: Define Your Subnets

This section is where you describe the subnets you need. Each subnet definition has three fields:

- **Name:** A label for the subnet (e.g., `web-public`, `private-db`)
- **Type:** Either `public` (routed via Internet Gateway) or `private` (routed via NAT Gateway)
- **IPs Required:** How many usable IP addresses you need in this subnet

As you type, a **live summary table** below the list updates to show:

- The smallest valid CIDR block that fits your IP requirement
- The total number of IPs in that block (including AWS reserved ones)
- The number of usable IPs after AWS reserves 5 per subnet

Click **+ Add Subnet** to add more rows. Click the **x** button to remove a row.

**Note:** AWS reserves 5 IP addresses in every subnet (network address, VPC router, DNS, future use, and broadcast). The calculator accounts for this automatically.

---

### Step 2: Configure VPC and Availability Zones

**VPC Base IP**

Enter the base IP address for your VPC in the format `10.0.0.0` (or any valid private IP range). This is the starting point for CIDR allocation.

Common private ranges:
- `10.0.0.0` (supports /8 to /28)
- `172.16.0.0` (supports /12 to /28)
- `192.168.0.0` (supports /16 to /28)

**VPC Prefix**

Select the prefix length (e.g., `/16`, `/20`, `/24`) from the dropdown. The tool immediately tells you whether the selected prefix is large enough to hold all your subnets across all AZs, using a colour-coded hint bar:

- **Green:** Fits well, with headroom
- **Yellow:** Tight, with little room for growth
- **Red:** Too small; the VPC cannot hold all subnets

A "Try /XX instead" nudge appears above the dropdown when a smaller prefix would still work.

**Number of Availability Zones**

Choose 1, 2, 3, or 4 AZs. Each AZ gets its own copy of the subnet set you defined in Step 1.

**Per-AZ Subnet Toggles**

After selecting the number of AZs, toggle chips appear for each AZ. Each chip represents one subnet. Toggle a chip off to exclude that subnet from that particular AZ. This is useful for architectures where, for example, only AZ-1 and AZ-2 should have a database subnet.

---

### Step 3: Calculate

Click the **Calculate Subnets** button.

The results section displays the following panels.

**Summary Box**

A quick overview covering total subnets calculated, total IPs allocated, total usable IPs, and wasted IPs due to block rounding.

**IP Allocation Bar**

A proportional horizontal bar showing how each subnet consumes the VPC address space. Hover over a segment to see the subnet name and CIDR.

**Per-AZ Result Tables**

One table per Availability Zone. Each row shows:

- Subnet type (public / private)
- Subnet name
- Assigned CIDR (e.g., `10.0.0.0/26`); click it to open the IP breakdown popover
- Full IP range (first to last address)
- Total IPs in the block
- Usable IPs

**IP Breakdown Popover**

Clicking any CIDR value opens a detailed popover listing every significant IP address in that subnet: the network address, the AWS-reserved addresses, a sample of usable addresses, and the broadcast address.

**Architecture Diagram**

A visual diagram shows:
- AWS Cloud and Region boundaries
- The VPC block and its CIDR
- Each AZ as a column
- Public subnets in blue, private subnets in green
- Internet Gateway (for VPCs with public subnets)
- NAT Gateway (for AZs with both public and private subnets)
- Route table annotations per subnet

---

### Quiz Mode

Before or after calculating, click **Enter Quiz Mode**. The results for each AZ are hidden behind an overlay. For each AZ, guess the prefix of the largest subnet (e.g., `/26`), then click **Check Answer**.

- A correct guess shows a green confirmation
- An incorrect guess reveals the right answer in red

Click **Reveal Results** on any AZ card to uncover that AZ's full results. Exiting quiz mode restores all results immediately.

---

### Share via URL

After calculating, the current configuration (VPC base IP, prefix, number of AZs, and all subnet definitions) is encoded into the URL query string. Copy the URL from your browser's address bar to share or bookmark your configuration. Opening that URL restores the exact same state.

---

## Hosting on GitHub Pages

This project is a single `.html` file with no build step required.

### Steps

1. Fork or clone this repository
2. Push the `vpc-calculator.html` file to the `main` (or `master`) branch of your GitHub repository
3. Rename the file to `index.html` if you want it to load at the root URL
4. Go to your repository on GitHub
5. Open **Settings** > **Pages**
6. Under **Source**, select **Deploy from a branch**
7. Choose your branch (e.g., `main`) and the root folder (`/`)
8. Click **Save**
9. GitHub will publish the site at `https://your-username.github.io/your-repo-name`

No dependencies, no npm install, and no build tools are needed.

---

## CIDR Concepts Reference

This tool covers the following networking concepts.

**CIDR (Classless Inter-Domain Routing)** is a method for allocating IP addresses using a base IP and a prefix length (e.g., `10.0.0.0/24`). The prefix defines how many bits are fixed (the network portion) and how many are available for hosts.

**Prefix to Block Size**

| Prefix | Total IPs | Usable IPs (AWS) |
|---|---|---|
| /16 | 65,536 | 65,531 |
| /20 | 4,096 | 4,091 |
| /24 | 256 | 251 |
| /26 | 64 | 59 |
| /28 | 16 | 11 |

**AWS Reserved IPs per Subnet**

AWS always reserves 5 IPs in every subnet:
1. `.0` - Network address
2. `.1` - VPC router
3. `.2` - AWS DNS
4. `.3` - Reserved for future use
5. Last address - Broadcast

**Public vs Private Subnet**

A public subnet has a route to an Internet Gateway. A private subnet routes outbound traffic through a NAT Gateway, which sits in a public subnet.

**Availability Zone (AZ)**

An Availability Zone is a physically isolated data centre within an AWS Region. Distributing subnets across multiple AZs improves fault tolerance.

---

## Browser Compatibility

The calculator works in any modern browser. No internet connection is required after the initial page load. Fonts are loaded from Google Fonts on the first visit and cached for offline use afterward.

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

---

## License

This project is open source and available under the [MIT License](LICENSE).

---

## Contributing

Contributions are welcome. Please open an issue first to discuss changes, then submit a pull request.

---

*This tool is independently developed for educational purposes. It is not affiliated with, sponsored by, or endorsed by Amazon Web Services, Inc.*
