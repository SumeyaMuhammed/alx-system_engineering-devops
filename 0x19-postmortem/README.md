# Postmortem: Web Application Outage on August 10, 2024

## Issue Summary

- **Duration of the Outage:**  
  The outage lasted from 2:00 PM UTC to 4:30 PM UTC on August 10, 2024.

- **Impact:**  
  During the outage, approximately 60% of users were unable to access the web application. Users experienced 502 Bad Gateway errors when attempting to load the main dashboard and check out items in the e-commerce store. API response times also increased by over 300%, causing delays in services that rely on the API for data fetching.

- **Root Cause:**  
  The root cause was traced back to a memory leak in the web application's backend server. The leak caused the application to consume excessive amounts of memory, eventually leading to the application becoming unresponsive under heavy traffic.

## Timeline

- **2:00 PM UTC:**  
  Datadog triggered an alert indicating high memory usage on the primary backend server.

- **2:05 PM UTC:**  
  An engineer began investigating logs and system performance metrics. Initial assumption pointed towards a database issue due to recent updates in database schema.

- **2:15 PM UTC:**  
  A more detailed analysis revealed that the memory usage was consistently increasing, but the database queries were not the cause. Investigation shifted to the application layer.

- **2:25 PM UTC:**  
  The application logs showed abnormal behavior in a function related to session management, with memory allocation steadily increasing for each user session.

- **2:40 PM UTC:**  
  The incident was escalated to the backend development team for further debugging. Meanwhile, the affected server was isolated to prevent further user impact.

- **3:30 PM UTC:**  
  The backend team identified a memory leak in the session management module, introduced during a recent update. The team created a hotfix to resolve the issue.

- **4:00 PM UTC:**  
  The hotfix was deployed, and the server was restarted. Memory consumption returned to normal levels, and API response times began to stabilize.

- **4:30 PM UTC:**  
  All systems were back online, and full service was restored. Monitoring continued to ensure stability.

## Root Cause and Resolution

- **Root Cause:**  
  The issue stemmed from a memory leak in the session management function of the backend server. Every time a new user session was created, memory was allocated but not properly released when the session ended. This caused the server to use up more RAM over time, eventually leading to a system crash as memory resources were exhausted.

- **Resolution:**  
  After identifying the memory leak, a hotfix was implemented. The code was updated to ensure that memory was properly freed when sessions were terminated. The server was restarted to clear the excessive memory usage, and the fix was deployed across all instances. Monitoring confirmed that memory usage remained stable post-deployment.

## Corrective and Preventative Measures

- **Improvements/Fixes:**
  - Codebase review to ensure proper memory management across the application.
  - Improved monitoring alerts for memory usage and session handling to catch similar issues sooner.
  - Regular load testing and stress testing to identify potential memory issues under heavy traffic.

- **Tasks to Address the Issue:**
  - Refactor session management code to improve memory efficiency.
  - Implement unit and integration tests specifically targeting memory usage.
  - Add memory usage dashboards to Datadog to track the application's performance in real time.
  - Schedule regular audits of third-party dependencies to ensure they are up-to-date and compatible with our system.
