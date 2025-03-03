# Postmortem: Unexpected Downtime on Web Service

![My Image](https://drive.google.com/uc?export=view&id=19-Vsvphe-bAU7rfFm2reRWY4sMZ5USBK)

## Service Downtime

### Issue Summary

- **Duration:**

  - **Start Time:** February 10, 2025, 09:00 (UTC)
  - **End Time:** February 10, 2025, 10:30 (UTC)

- **Impact:**

  - Users experienced intermittent **HTTP 503** errors when accessing the service.
  - Approximately **30% of users** were unable to load the web application during the outage.
  - The outage stemmed from an **expired SSL certificate**, causing **Nginx to terminate TLS handshakes** abruptly.

- **Root Cause:**

  - An **auto-renewal failure** for the SSL certificate led to expiration, disrupting **secure connections on port 443**.

---

## Timeline

### Detection

- **09:00 UTC:** Monitoring tools triggered an alert due to a spike in **TLS handshake failures**.

### Actions Taken

- Reviewed **Nginx error logs**, which indicated **SSL certificate validation errors**.
- Checked **server resource usage** (CPU, memory) to rule out overload.
- Verified **DNS configurations and CDN health**.

### Misleading Paths

- Initially suspected a **DDoS attack** due to the sudden spike in failed connections.

- Investigated **CDN configurations**, delaying identification of the certificate issue.

### Escalation

- **09:40 UTC:** Escalated the incident to the **Security Team** to rule out malicious activity.

### Resolution

- **10:15 UTC:** Renewed the SSL certificate manually.
- **10:30 UTC:** Reloaded Nginx to apply changes, restoring service.

---

## Root Cause and Resolution

### Root Cause

- The **SSL certificate expired** due to a **misconfigured cron job** responsible for automated renewal.
- The cron job **lacked permissions** to write to the certificate directory.

### Resolution

- **Renewed the certificate** using **Certbot** and adjusted cron job permissions.
- Implemented **immediate monitoring** for SSL certificate expiration dates.

---

## Corrective and Preventative Measures

### Improvements/Fixes

#### **Automation Enhancements**

- Update **certificate renewal scripts** to validate **write permissions** and send alerts on **renewal failures**.

#### **Monitoring Upgrades**

- Add **SSL certificate expiration checks** to the monitoring stack with a **30-day warning threshold**.

#### **Documentation**

- Revise the **DevOps playbook** to include **certificate renewal troubleshooting steps**.

---

## Tasks

- **Audit all cron jobs** for proper permissions and logging (**Due: Feb 28, 2025**).
- **Conduct a workshop** on SSL/TLS best practices for the engineering team (**Due: March 5, 2025**).
- **Integrate Let’s Encrypt Certbot** into the deployment pipeline for auto-renewal (**Due: March 15, 2025**).

---

## Conclusion

The outage was caused by an **expired SSL certificate due to a failed auto-renewal process**. After manually renewing the certificate and adjusting permissions, the service was restored. Moving forward, enhanced **automation, monitoring, and documentation** will prevent similar incidents.
