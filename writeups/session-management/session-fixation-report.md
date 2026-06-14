## Vulnerability Report: Session Identifier Not Regenerated After Authentication

**Target:** `https://sp.srmist.edu.in/`  
**Vulnerability Type:** Session Management Weakness / Potential Session Fixation  
**Severity:** Low to Medium

---

### What Is the Vulnerability?  
When a user logs in, the application should issue a **new session ID** to mark the transition from "anonymous visitor" to "authenticated user." This application does not do that — the same `JSESSIONID` assigned before login is kept after login.

This means any session ID that existed before authentication remains fully valid after it, which opens the door to **session fixation** attacks.

---

### PoC - Proof of Concept
- Two different incognito browser(brave, firefox) sessions, They both have different `JSESSIONID` before Login.  

    ![](/assets/7.png)

- Step 1 - Note the assigned pre-login `JSESSIONID` in the first browser  
    
    ![](/assets/8.png)

- Step 2 - Log in and check the `JSESSIONID` again.  
    
    ![](/assets/9.png)
After a successful login, inspect the session cookie. It is **unchanged**:

    ```
    Before login: JSESSIONID=BCDBA3FB2CEB1EFE953FE0727562CF8C.worker1
    After login:  JSESSIONID=BCDBA3FB2CEB1EFE953FE0727562CF8C.worker1
    ```

- Step 3 — Manually paste the same `JSESSIONID` in the second browser.  
    
    ![](/assets/10.png)

    Now navigate to an authenticated page `/template/HRDSystem.jsp` by changing the path.  
    
    ![](/assets/11.png)  

- **Result:** The second browser loads the authenticated session — no credentials needed.  
    
    ![](/assets/12.png) 


### Step 4 — Verify logout behaviour

Log out from either browser. Both browsers lose access immediately and are redirected to the login page.

This confirms both browsers were sharing the exact same server-side session throughout.

---

### Observed Behaviour Summary

| Behaviour | Status |
|---|---|
| Session ID assigned before login | ✅ Yes |
| Session ID regenerated after login | ❌ No |
| Same session reusable across browsers | ✅ Yes (confirmed) |
| Session invalidated on logout | ✅ Yes (working correctly) |

---

### Security Impact

Authentication is a privilege change. Best practice require issuing a new session ID at that moment.

Because this application skips that step:  
- A session ID created before login stays valid after login.  
- Anyone who knows that session ID can access the authenticated session.  
- If an attacker can **plant a known session ID** on a victim's browser (e.g. via a shared network, XSS, or a URL parameter on some frameworks), they can wait for the victim to log in and then take over the session.

No direct account takeover was demonstrated during this test, but the vulnerability lowers the bar for one significantly.

---

### Remediation

Regenerate the session ID immediately after a successful login, before any authenticated content is served.

Most frameworks handle this with a single configuration or method call:  
- **Spring Security (Java):** `SessionManagementConfigurer.sessionFixation().newSession()` or `.changeSessionId()`  
- **Jakarta EE / Servlet:** Call `request.getSession(false).invalidate()` then `request.getSession(true)` after login  
- **General:** Never carry the same session ID across an authentication boundary

---

### References

- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [CWE-384: Session Fixation](https://cwe.mitre.org/data/definitions/384.html)
- [OWASP Top 10: A07 — Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)