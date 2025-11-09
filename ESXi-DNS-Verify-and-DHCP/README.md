# Verify DNS & Install/Authorize DHCP (+ first scope)

Host: `fna-srv-dc01` (Windows Server 2022)  
Domain: `fna.test` • Network: `10.0.0.0/24` • DC/DNS IP: `10.0.0.21`  
Purpose: Verify AD-integrated DNS (forward + reverse), create PTR + validate NS, then install/authorize DHCP Server and create the first IPv4 scope.

## Part A — DNS verification

1. Open DNS Manager (Server Manager → Tools → DNS).  
   ![Open DNS MMC](images/01-open-dns-mmc.png)

2. Confirm the DNS server is present.  
   ![DNS console open](images/02-dns-console-open.png)

3. Check Forward Lookup Zones (`_msdcs.fna.test` and `fna.test`) for SOA/NS + host records.  
   ![Forward zone records](images/03-forward-zone-fna-test.png)

### Create reverse lookup zone (10.0.0.x)

4. Reverse Lookup Zones → New Zone…  
   ![Start new reverse zone](images/04-new-reverse-zone-start.png)

5. Primary zone + Store in Active Directory.  
   ![Zone type](images/05-new-zone-type-primary.png)

6. Replication: To all DNS servers on DCs in this domain (fna.test).  
   ![Replication scope](images/06-replication-scope.png)

7. Network ID: 10.0.0.  
   ![Network ID](images/07-reverse-zone-networkid.png)

8. Dynamic updates: Allow only secure.  
   ![Dynamic updates](images/08-dynamic-updates-secure.png)

9. Finish the wizard.  
   ![Finish](images/09-new-zone-finish.png)

### Create/confirm PTR for the DC

10. Open Properties of `fna-srv-dc01` A record.  
    ![Open A record](images/10-a-record-properties.png)

11. Ensure Update associated PTR is checked (IP `10.0.0.21`).  
    ![A record dialog](images/11-a-record-properties-dialog.png)

12. Verify PTR exists in the reverse zone.  
    ![PTR present](images/12-reverse-zone-ptr-created.png)

### Validate Name Server list

13. Reverse zone Properties.  
    ![Reverse zone properties](images/13-reverse-zone-properties-menu.png)

14. Name Servers shows `fna-srv-dc01.fna.test`.  
    ![NS tab](images/14-reverse-zone-name-servers.png)

15. Edit and validate IPv4 10.0.0.21 (OK).  
    ![Edit NS record](images/15-edit-name-server-record.png)

---

## Part B — Install & authorize DHCP

16. Add Roles and Features → select DHCP Server.  
    ![Select DHCP](images/16-install-dhcp-select-role.png)

17. Add required management tools.  
    ![Add features](images/17-install-dhcp-add-features.png)

18. Features screen (defaults OK).  
    ![Features](images/18-install-dhcp-features.png)

19. Confirm install (auto-restart if needed).  
    ![Confirm](images/19-install-dhcp-confirm.png)

20. Installation progress.  
    ![Progress](images/20-install-dhcp-progress.png)

21. In Server Manager, click Complete DHCP configuration.  
    ![Post-deploy banner](images/21-complete-dhcp-config-banner.png)

22. Authorize with FNA\Administrator.  
    ![Authorize](images/22-dhcp-authorize-credentials.png)

23. Summary shows Creating security groups + Authorizing DHCP server: Done.  
    ![Post-install summary](images/23-dhcp-postinstall-summary.png)

---

## Part C — Create first IPv4 scope

24. Open DHCP (Server Manager → Tools → DHCP).  
    ![Open DHCP MMC](images/24-open-dhcp-mmc.png)

25. Right-click IPv4 → New Scope…  
    ![New scope](images/25-new-scope-context.png)

26. Name: FNAIPv4.  
    ![Scope name](images/26-scope-name.png)

27. Range: 10.0.0.50 – 10.0.0.200, mask /24.  
    ![Range](images/27-scope-range.png)

28. Exclusions: e.g., 10.0.0.120 (or whatever you reserved).  
    ![Exclusions](images/28-scope-exclusions.png)

29. Lease duration: 8 days (default).  
    ![Lease duration](images/29-lease-duration.png)

30. Choose Yes, configure these options now.  
    ![Configure options now](images/30-configure-options-now.png)

31. Router (Option 003): `10.0.0.1`.  
    ![Router option](images/31-option-router.png)

32. DNS (Option 006/015): Parent domain `fna.test`, DNS server `10.0.0.21`.  
    ![DNS option](images/32-option-dns.png)

33. WINS (optional): none.  
    ![WINS option](images/33-option-wins.png)

34. Activate the scope.  
    ![Activate scope](images/34-activate-scope.png)

35. Scope shows Active under IPv4.  
    ![Scope active](images/35-scope-active.png)

---

## Validation from a workstation

36. On `fna-ws11-01`, `ipconfig` shows an address from the scope (e.g., 10.0.0.51) + default gateway 10.0.0.1.  
    ![Client ipconfig](images/36-client-ipconfig.png)

37. `ipconfig /all` confirms DHCP Enabled: Yes, DHCP Server: 10.0.0.21, and DNS Servers include 10.0.0.21.  
    ![Client ipconfig /all](images/37-client-ipconfig-all.png)

---

## What this proves

- AD-integrated DNS is healthy (forward + reverse, PTR, NS).  
- DHCP Server is installed, authorized, and handing out leases from a well-scoped range.  
- Clients receive correct gateway and DNS settings—ready for domain joins and GPO work.
