# Group Policy and Managing Accounts

## Overview
This repository documents how to configure Group Policy to manage account lockouts, enable/disable accounts, and observe logs within Active Directory. These steps are essential for securing and managing user accounts in an enterprise environment.

## Prerequisites
- **Windows Server (Domain Controller - DC-1)**
- **Windows Client Machine (Domain-Joined)**
- **Active Directory Users and Computers (ADUC)**
- **Group Policy Management Console (GPMC)**

## Step 1: Dealing with Account Lockouts
1. **Log into DC-1** (Domain Controller).
2. **Select a random user account** that was previously created.

![image](https://github.com/user-attachments/assets/a9d11703-50af-4470-9599-f7c3e6e611bd)

3. **Attempt to log in 10 times** with an incorrect password.

![image](https://github.com/user-attachments/assets/e78a1a9d-f08a-44ef-b9bc-907c0f753a3b)
![Screenshot 2025-03-20 200712](https://github.com/user-attachments/assets/54743f48-f257-4d58-99e1-be2f3c17a3dd)

Even after multiple failed password attempts, the user can still log in. This shows that account lockout isn't yet enforced — we’ll configure it using Group Policy.

4. **Configure Group Policy to lock the account after 5 failed attempts**:
   - Open **Group Policy Management** (`gpmc.msc`)

![Screenshot 2025-03-20 202509](https://github.com/user-attachments/assets/f9f61b5c-7f1b-41f4-9837-347d91a01eb7)

   - Navigate to **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy**
 
![Screenshot 2025-03-20 202859](https://github.com/user-attachments/assets/17ea3bc9-cfc1-4828-a8b1-3751fb9ea3ce)
   
   - Set **Account Lockout Threshold** to **5 invalid logon attempts**
  
![Screenshot 2025-03-20 203129](https://github.com/user-attachments/assets/c1d438d8-51fd-4a10-afbe-900ccfbd8be1)
   
   - Set **Reset account lockout counter after** to **30 minutes**
 
   ![image](https://github.com/user-attachments/assets/1336c505-5c01-4309-82cb-769014815227)

   - Set **Account Lockout Duration** to **30 minutes**
  
![Screenshot 2025-03-20 203353](https://github.com/user-attachments/assets/2cd5d8d7-a0d2-4dbe-a1b2-0e4ed159fa08)
   
   - Apply the policy and force an update with `gpupdate /force`

To apply the new policy, we'll log in to client-1 using Jane's credentials, since she has administrative access. Open Command Prompt, and run the update. Running gpupdate /force ensures the new Group Policy settings are applied immediately without needing to reboot.

![Screenshot 2025-03-20 204809](https://github.com/user-attachments/assets/aaa21877-d992-4e90-ae6e-e2ab74daca00)

![Screenshot 2025-03-20 204952](https://github.com/user-attachments/assets/70d6fba0-05b9-4810-95c7-a472e96444a3)

![Screenshot 2025-03-20 205106](https://github.com/user-attachments/assets/df1b4294-5197-45f4-9e27-627095aad191)

5. **Attempt to log in again 6 times** with a bad password.


 ![Screenshot 2025-03-20 205812](https://github.com/user-attachments/assets/be5f9433-3710-4320-8192-0d26660c423c)

6. Observe that the account has been **locked out** in **Active Directory Users and Computers (ADUC)**.

![Screenshot 2025-03-20 205812](https://github.com/user-attachments/assets/f56044e9-e491-41d4-891f-abab27a2a358)

7. **Unlock the account**:
   - Open **Active Directory Users and Computers**
   - Locate the locked-out account
   - Right-click > **Properties** > **Account** tab
   - Click **Unlock Account** and reset the password if necessary.

![Screenshot 2025-03-20 211802](https://github.com/user-attachments/assets/65f9d2ce-e1ae-49d2-9659-64ab6f53849d)

![Screenshot 2025-03-20 211828](https://github.com/user-attachments/assets/98551da9-dd27-437e-8a11-84a9587c8745)

8. **Attempt to log in with the new credentials**.

![Screenshot 2025-03-20 212256](https://github.com/user-attachments/assets/40578470-08a4-4a03-b2ed-b5c7edac0000)

The account has been unlocked and we've reset the password. The user can now log in.


## Step 2: Enabling and Disabling Accounts

1. **Disable the same user account in Active Directory**:
   - Open **Active Directory Users and Computers**
   - Locate the user account, right-click, and select 
   **Disable Account**.

![Screenshot 2025-03-20 212602](https://github.com/user-attachments/assets/c57aca2d-a27b-4c0f-b8ca-fd2eadf367d8)

2. **Attempt to log in with the disabled account** and observe the error message.

![Screenshot 2025-03-20 212822](https://github.com/user-attachments/assets/3ff4f13f-3606-4b6c-a702-2fdfadfb4b01)

3. **Re-enable the account**:
   - Right-click the disabled account and select **Enable Account**.

![Screenshot 2025-03-20 212915](https://github.com/user-attachments/assets/0d46d459-0583-4652-97b6-ceebc9c8945f)

![Screenshot 2025-03-20 212939](https://github.com/user-attachments/assets/b76f1b03-af42-4fbf-932e-bd79ced8a7d1)

4. **Attempt to log in again** and verify successful access.

![Screenshot 2025-03-20 213212](https://github.com/user-attachments/assets/cff781fe-f16a-4090-8fd7-3c2e066a7a94)


## Step 3: Observing Logs
1. **Check logs on the Domain Controller**:
   - Open **Event Viewer** (`eventvwr.msc`)
   - Navigate to **Windows Logs > Security**
   - Look for Event ID **4740** (Account Lockout) and **4625** (Failed Logon Attempts).

![Screenshot 2025-03-20 214902](https://github.com/user-attachments/assets/4b355566-296b-4e9b-a122-f0c361ba8cc6)

![Screenshot 2025-03-20 215426](https://github.com/user-attachments/assets/cda79988-24ac-4a55-a93e-347e82cc39a8)


2. **Check logs on the Client Machine**:
   - Open **Event Viewer** (`eventvwr.msc`)
   - Navigate to **Windows Logs > Security**
   - Look for failed login attempts and account status changes.

![Screenshot 2025-03-20 221647](https://github.com/user-attachments/assets/03f34e5a-cbc4-48d0-994b-b5fbb42b3d39)

![Screenshot 2025-03-20 221854](https://github.com/user-attachments/assets/fcb0cfba-e74d-44ba-8acc-26679cd5f35a)

From client-1, while logged in as Jane, we see the failed logon attempts from our user.

## Important Considerations
- **Account Lockout Threshold**: Setting too low (e.g., 1 or 2 attempts) may lead to frequent lockouts.
- **Account Lockout Duration**: A high setting may be inconvenient but improves security.
- **Reset Account Lockout Counter After**: If too short, an attacker could repeatedly attempt logins without triggering a lockout.


## Conclusion
This guide ensures that Group Policy is properly configured to manage account lockouts and user access within an Active Directory environment. Monitoring Event Logs helps in tracking login issues and security events efficiently.

