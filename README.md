# DNS A-Records and CNAME Records Lab Documentation

## Lab Overview
This lab demonstrates DNS record management including A-Records and CNAME records, DNS caching behavior, and DNS resolution troubleshooting using Windows Server Active Directory environment.

## Prerequisites
- Azure subscription with DC-1 and Client-1 VMs configured
- DC-1: Windows Server with DNS Server role installed
- Client-1: Windows client joined to the domain
- Domain admin account (mydomain.com\jane_admin)

## Environment Setup
1. **Start Azure VMs**: Ensure DC-1 and Client-1 VMs are powered on in Azure Portal
2. **Verify Network Connectivity**: Confirm both VMs can communicate on the virtual network

---

## A-Record Exercise

### Steps

#### 1. Connect/log into DC-1 as your domain admin account (mydomain.com\jane_admin)
- RDP to DC-1 using domain admin credentials
- Username: `mydomain.com\jane_admin`
- Password: [Your configured password]

#### 2. Connect/log into Client-1 as an admin (mydomain\jane_admin)
- RDP to Client-1 using domain admin credentials  
- Username: `mydomain\jane_admin`
- Password: [Your configured password]

#### 3. From Client-1 try to ping "mainframe" notice that it fails

![Screenshot 2025-05-28 145622](https://github.com/user-attachments/assets/b297d525-7796-42bc-8734-ad0b7e37dd6c)

On Client-1, open Command Prompt as Administrator:

```cmd
ping mainframe
```
**Observation**: Ping fails - hostname cannot be resolved

#### 4. Nslookup "mainframe" notice that it fails (no DNS record)
```cmd
nslookup mainframe
```
**Observation**: DNS lookup fails - no DNS record exists

#### 5. Create a DNS A-record on DC-1 for "mainframe" and have it point to DC-1's Private IP address


On DC-1:
1. Open **DNS Manager** (Start → Administrative Tools → DNS)
2. Expand server name → Forward Lookup Zones → mydomain.com
3. Right-click in the zone → **New Host (A or AAAA)**
4. Configure new A-Record:
   - **Name**: `mainframe`
   - **IP Address**: [DC-1's Private IP Address]
   - Check **Create associated pointer (PTR) record** if desired
5. Click **Add Host**
6. Click **OK** to confirm creation

#### 6. Go back to Client-1 and try to ping it. Observe that it works
Return to Client-1 Command Prompt:

```cmd
ping mainframe
```
**Observation**: Ping succeeds, resolves to DC-1's IP address

---

## Local DNS Cache Exercise

### Steps

#### 1. Go back to DC-1 and change mainframe's record address to 8.8.8.8
On DC-1:
1. In DNS Manager, locate the "mainframe" A-Record
2. Double-click the record to edit
3. Change IP address to: `8.8.8.8`
4. Click **OK** to save changes

#### 2. Go back to Client-1 and ping "mainframe" again. Observe that it still pings the old address
```cmd
ping mainframe
```
**Observation**: Still pings the old IP address (DC-1's IP) due to local DNS cache

#### 3. Observe the local dns cache (ipconfig /displaydns)
```cmd
ipconfig /displaydns
```
**Observation**: Shows cached DNS entries including the old "mainframe" record

#### 4. Flush the DNS cache (ipconfig /flushdns)



```cmd
ipconfig /flushdns
```
**Observation**: "Successfully flushed the DNS Resolver Cache"

#### 5. Observe that the cache is empty (ipconfig /displaydns)
```cmd
ipconfig /displaydns
```
**Observation**: Minimal or no cached entries displayed

#### 6. Attempt to ping "mainframe" again. Observe the address of the new record is showing up
```cmd
ping mainframe
```
**Observation**: Now pings the new IP address (8.8.8.8)

![Screenshot 2025-05-28 145833](https://github.com/user-attachments/assets/58d1a19c-dbbf-45ca-a5ea-55813a76902c)

---

## CNAME Record Exercise

### Steps

#### 1. Go back to DC-1 and create a CNAME record that points the host "search" to "www.google.com"
On DC-1:
1. In DNS Manager, navigate to mydomain.com forward lookup zone
2. Right-click → **New Alias (CNAME)**
3. Configure CNAME record:
   - **Alias name**: `search`
   - **Fully qualified domain name (FQDN) for target host**: `www.google.com`
4. Click **OK** to create the record

#### 2. Go back to Client-1 and attempt to ping "search", observe the results of the CNAME record
On Client-1:
```cmd
ping search
```
**Observation**: Resolves "search" to Google's IP address and attempts to ping

![Screenshot 2025-05-28 145920](https://github.com/user-attachments/assets/7a21a039-ac28-466b-9bfd-b1661309940e)

#### 3. On Client-1, nslookup "search", observe the results of the CNAME record
```cmd
nslookup search
```
**Observation**: Shows the CNAME record pointing to www.google.com and the resolved IP address

---

## Key Learning Points

### DNS A-Records
- **Purpose**: Map hostnames directly to IP addresses
- **Use Case**: Direct hostname-to-IP resolution
- **Format**: hostname → IP address

### DNS Caching
- **Local Cache**: Windows maintains a local DNS cache for performance
- **Cache Persistence**: Cached entries persist until TTL expires or manual flush
- **Management Commands**:
  - `ipconfig /displaydns` - View cache contents
  - `ipconfig /flushdns` - Clear cache

### CNAME Records
- **Purpose**: Create aliases that point to other hostnames
- **Use Case**: Hostname redirection and aliasing
- **Format**: alias → canonical hostname
- **Resolution**: Two-step process (alias → canonical name → IP address)

## Troubleshooting Tips

### Common Issues
1. **DNS Resolution Failures**
   - Verify DNS server configuration
   - Check network connectivity
   - Confirm DNS records exist

2. **Caching Problems**
   - Flush local DNS cache
   - Check DNS server cache
   - Verify TTL settings

3. **CNAME Issues**
   - Ensure target hostname is resolvable
   - Avoid CNAME chains (CNAME pointing to CNAME)
   - Check for conflicts with other record types

### Verification Commands
```cmd
# Test basic connectivity
ping [hostname]

# DNS lookup with details
nslookup [hostname]

# Display local DNS cache
ipconfig /displaydns

# Clear local DNS cache
ipconfig /flushdns

# Test specific DNS server
nslookup [hostname] [dns-server-ip]
```

## Lab Cleanup
- **Do NOT delete the Azure VMs** - they will be used for future labs
- To save costs when not in use: Stop the VMs in Azure Portal
- DNS records can remain configured for future exercises

## Next Steps
These VMs and DNS configurations will be used in upcoming networking and Active Directory labs. The DNS infrastructure created here provides the foundation for more advanced directory services exercises.
