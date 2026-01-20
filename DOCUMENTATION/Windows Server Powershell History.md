
# 1/20

# Step 1: Enable Windows Defender Firewall on all profiles
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Step 2: Set default deny for all profiles (block all inbound/outbound by default)
Set-NetFirewallProfile -Profile Domain,Public,Private -DefaultInboundAction Block -DefaultOutboundAction Block

# Step 3: Enable firewall logging
Set-NetFirewallProfile -Profile Domain,Public,Private -LogAllowed True -LogBlocked True -LogFileName "%systemroot%\System32\LogFiles\Firewall\pfirewall.log" -LogMaxSizeKilobytes 16384

# ALLOW INBOUND RULES

# From VLAN20 (Tryton) - 192.168.20.0/24
New-NetFirewallRule -DisplayName "VLAN20-Kerberos-TCP88" -Direction Inbound -LocalPort 88 -Protocol TCP -RemoteAddress 192.168.20.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN20-LDAP-TCP389" -Direction Inbound -LocalPort 389 -Protocol TCP -RemoteAddress 192.168.20.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN20-LDAPS-TCP636" -Direction Inbound -LocalPort 636 -Protocol TCP -RemoteAddress 192.168.20.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN20-DNS-UDP53" -Direction Inbound -LocalPort 53 -Protocol UDP -RemoteAddress 192.168.20.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN20-TrytonAdmin-TCP443" -Direction Inbound -LocalPort 443 -Protocol TCP -RemoteAddress 192.168.20.0/24 -Action Allow -Profile Domain

# From VLAN50 (Users) - 192.168.50.0/24
New-NetFirewallRule -DisplayName "VLAN50-Kerberos-TCP88" -Direction Inbound -LocalPort 88 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN50-LDAP-TCP389" -Direction Inbound -LocalPort 389 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN50-LDAPS-TCP636" -Direction Inbound -LocalPort 636 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN50-SMB-TCP445" -Direction Inbound -LocalPort 445 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN50-RPC-TCP135" -Direction Inbound -LocalPort 135 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN50-RPC-Dynamic" -Direction Inbound -LocalPort 49152-65535 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "VLAN50-DNS-UDP53" -Direction Inbound -LocalPort 53 -Protocol UDP -RemoteAddress 192.168.50.0/24 -Action Allow -Profile Domain

# Admin RDP access only
New-NetFirewallRule -DisplayName "Admin-RDP-192.168.1.10" -Direction Inbound -LocalPort 3389 -Protocol TCP -RemoteAddress 192.168.1.10 -Action Allow -Profile Domain

# ALLOW OUTBOUND RULES

# To Wazuh SIEM - 172.168.10.0/24
New-NetFirewallRule -DisplayName "Wazuh-Agent-TCP1514" -Direction Outbound -RemotePort 1514 -Protocol TCP -RemoteAddress 172.168.10.0/24 -Action Allow -Profile Domain

New-NetFirewallRule -DisplayName "Wazuh-Agent-TCP1515" -Direction Outbound -RemotePort 1515 -Protocol TCP -RemoteAddress 172.168.10.0/24 -Action Allow -Profile Domain

# Response to Tryton on TCP 443 (stateful return traffic)
New-NetFirewallRule -DisplayName "Response-Tryton-TCP443" -Direction Outbound -RemotePort 443 -Protocol TCP -RemoteAddress 192.168.20.0/24 -Action Allow -Profile Domain

# EXPLICIT DENY RULES (logged for alerting)

# Block RDP from non-admin VLAN50 workstations
New-NetFirewallRule -DisplayName "DENY-RDP-VLAN50-NonAdmin" -Direction Inbound -LocalPort 3389 -Protocol TCP -RemoteAddress 192.168.50.0/24 -Action Block -Profile Domain

# Block Internet access (critical - indicates compromise if triggered)
# Assuming Internet gateway is default route - block common Internet ports
New-NetFirewallRule -DisplayName "DENY-Internet-HTTP-TCP80" -Direction Outbound -RemotePort 80 -Protocol TCP -Action Block -Profile Domain,Public,Private

New-NetFirewallRule -DisplayName "DENY-Internet-HTTPS-TCP443" -Direction Outbound -RemotePort 443 -Protocol TCP -Action Block -Profile Domain,Public,Private -RemoteAddress Any

# Block reverse connections to application servers (VLAN20)
New-NetFirewallRule -DisplayName "DENY-Outbound-VLAN20" -Direction Outbound -RemoteAddress 192.168.20.0/24 -Action Block -Profile Domain

# Block server initiating to clients (VLAN50)
New-NetFirewallRule -DisplayName "DENY-Outbound-VLAN50" -Direction Outbound -RemoteAddress 192.168.50.0/24 -Action Block -Profile Domain

# VERIFICATION

Write-Host "Firewall rules applied successfully!" -ForegroundColor Green
Write-Host "Verifying configuration..." -ForegroundColor Yellow

# Check firewall status
Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction

# Display created rules
Write-Host "`nInbound Rules:" -ForegroundColor Cyan
Get-NetFirewallRule | Where-Object {$_.DisplayName -like "VLAN*" -or $_.DisplayName -like "Admin-*"} | Select-Object DisplayName, Direction, Action

Write-Host "`nOutbound Rules:" -ForegroundColor Cyan
Get-NetFirewallRule | Where-Object {$_.DisplayName -like "Wazuh-*" -or $_.DisplayName -like "Response-*" -or $_.DisplayName -like "DENY-*"} | Select-Object DisplayName, Direction, Action

Write-Host "`nFirewall logging enabled at: %systemroot%\System32\LogFiles\Firewall\pfirewall.log" -ForegroundColor Green


