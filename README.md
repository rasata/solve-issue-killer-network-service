# Windows Service Diagnostic Tool

A PowerShell-based diagnostic tool designed to identify Windows services causing network performance issues, particularly slow upload speeds. This tool was created to systematically isolate problematic services through automated testing and user feedback.

## 🎯 Problem Solved

This tool was developed to diagnose and resolve network upload performance issues caused by conflicting Windows services. The primary culprit identified was **Killer Network Service** from Rivet Networks/Intel, which can significantly throttle upload speeds due to its "intelligent traffic prioritization" feature.

## 📁 Repository Contents

```
├── diagnostic-script.ps1          # Main diagnostic script (interactive service testing)
├── targeted-service-manager.ps1   # Targeted management of identified problem services
└── README.md                      # This documentation
```

## 🚀 Quick Start

### Prerequisites
- Windows 10/11
- PowerShell 5.0 or later
- Administrator privileges

### Basic Usage

1. **Clone the repository:**
```bash
git clone https://github.com/[your-username]/windows-service-diagnostic-tool.git
cd windows-service-diagnostic-tool
```

2. **Run as Administrator:**
```powershell
# Open PowerShell as Administrator
Right-click on PowerShell → "Run as Administrator"
```

3. **Execute the main diagnostic:**
```powershell
# Interactive diagnosis (recommended for first-time users)
.\diagnostic-script.ps1

# Quick targeted test for known problem services
.\targeted-service-manager.ps1 -Action individual
```

## 📋 Scripts Overview

### 1. `diagnostic-script.ps1` - Main Diagnostic Tool

**Purpose:** Systematically identifies problematic services by comparing safe mode vs normal mode service states.

**Features:**
- Creates baseline from safe mode services
- Identifies additional services running in normal mode
- Tests services in groups of 20 (configurable)
- Interactive user feedback for problem identification
- Automatic subdivision into smaller groups when problems are found
- Comprehensive logging

**Usage:**
```powershell
# Capture safe mode baseline (run in safe mode)
.\diagnostic-script.ps1 -Mode capture

# Analyze and test services (run in normal mode)
.\diagnostic-script.ps1 -Mode interactive

# Custom group size
.\diagnostic-script.ps1 -Mode interactive -GroupSize 10
```

### 2. `targeted-service-manager.ps1` - Problem Service Manager

**Purpose:** Manages the 5 specific services identified as potential network performance culprits.

**Target Services:**
- **Killer Network Service** (Primary suspect)
- LanmanServer (Windows file sharing)
- lfsvc (Geolocation service)
- LicenseManager (Windows Store licensing)
- LMS (Intel Management service)

**Usage:**
```powershell
# Interactive menu
.\targeted-service-manager.ps1

# Test each service individually
.\targeted-service-manager.ps1 -Action individual

# Disable non-critical services
.\targeted-service-manager.ps1 -Action disable

# View service status
.\targeted-service-manager.ps1 -Action status

# Re-enable all services
.\targeted-service-manager.ps1 -Action enable
```

## 🔍 Diagnostic Process

### Method 1: Full Diagnostic (First Time)

1. **Safe Mode Capture:**
   - Boot into Safe Mode with Networking
   - Run: `.\diagnostic-script.ps1 -Mode capture`
   - Reboot to normal mode

2. **Interactive Testing:**
   - Run: `.\diagnostic-script.ps1 -Mode interactive`
   - Follow prompts to test service groups
   - System will guide you through identifying the problematic service

3. **Problem Resolution:**
   - Once identified, disable the problematic service
   - Test your network performance
   - Use targeted manager for fine-tuned control

### Method 2: Quick Test (Known Issues)

1. **Direct Testing:**
   ```powershell
   .\targeted-service-manager.ps1 -Action individual
   ```

2. **Follow the prompts** to test each suspected service individually

3. **Disable the culprit** when identified

## ⚠️ Important Warnings

### Critical Services
- **LanmanServer:** Required for file/printer sharing
- **Other Windows services:** Marked as critical in the script

### Before Running
- Create a system restore point
- Ensure you have administrator access
- Close important applications
- Test during non-critical hours

### If Something Goes Wrong
```powershell
# Restore all services
.\targeted-service-manager.ps1 -Action enable

# Or manually via services.msc
services.msc
```

## 🎯 Common Culprits

### Killer Network Service (Primary Target)
**Symptoms:**
- Extremely slow upload speeds
- Normal download speeds
- Network "optimization" software installed

**Solution:**
1. Disable the service using this tool
2. Uninstall Killer software completely
3. Use generic Intel network drivers

**Manual Removal:**
```powershell
# Stop and disable Killer service
Stop-Service -Name "Killer Network Service" -Force
Set-Service -Name "Killer Network Service" -StartupType Disabled

# Uninstall via Control Panel → Programs and Features
# Look for "Killer" or "Rivet Networks" software
```

### Other Potential Issues
- **Intel Management Engine:** Can cause conflicts
- **Geolocation services:** Unnecessary network traffic
- **Windows Store services:** Background activities

## 📊 Logging and Output

### Log Locations
- Main diagnostic: `C:\Temp\ServiceDiagnostic\DiagnosticLog.txt`
- Targeted manager: `C:\Temp\ServiceDiagnostic\TargetedServiceLog.txt`
- Service snapshots: `C:\Temp\ServiceDiagnostic\*.json`

### Log Contents
- Timestamp of all actions
- Services modified
- User decisions
- Error messages
- Final recommendations

## 🛠️ Customization

### Group Sizes
```powershell
# Test in groups of 30 services
.\diagnostic-script.ps1 -GroupSize 30

# Test in groups of 5 services (more precise)
.\diagnostic-script.ps1 -GroupSize 5
```

### Custom Log Path
```powershell
.\diagnostic-script.ps1 -LogPath "D:\MyLogs"
```

### Adding New Problem Services
Edit the `$ProblemServices` array in `targeted-service-manager.ps1`:

```powershell
$ProblemServices += @{
    Name = "Your Service Name"
    ServiceName = "ActualServiceName"
    Description = "Description of what it does"
    Critical = $false
    Recommendation = "Your recommendation"
}
```

## 🔧 Troubleshooting

### Script Won't Run
```powershell
# Check execution policy
Get-ExecutionPolicy

# Allow script execution (if needed)
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### Permission Errors
- Ensure PowerShell is running as Administrator
- Check if services are protected by group policy

### Services Won't Stop
- Some services may have dependencies
- Check the log file for specific error messages
- Try manual service management via `services.msc`

### Network Still Slow
1. Verify the correct service was disabled
2. Restart your computer
3. Check for other network optimization software
4. Consider driver updates

## 🤝 Contributing

### Reporting Issues
When reporting issues, please include:
- Windows version
- PowerShell version
- Complete error messages
- Log file contents
- Network hardware information

### Feature Requests
- Additional service detection
- GUI interface
- Network speed testing integration
- Automatic driver suggestions

### Development Setup
```powershell
# Clone and test
git clone [repository-url]
cd windows-service-diagnostic-tool

# Test in a VM first
# Always backup before testing
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⚡ Quick Reference

### Emergency Commands
```powershell
# Stop Killer Network Service immediately
Stop-Service -Name "Killer Network Service" -Force
Set-Service -Name "Killer Network Service" -StartupType Disabled

# Restore all services if needed
.\targeted-service-manager.ps1 -Action enable

# Check service status
Get-Service | Where-Object {$_.Name -like "*Killer*"}
```

### Performance Testing
```powershell
# Test network speed before/after
# Use online speed tests or:
Test-NetConnection -ComputerName google.com -Port 80
```

## 🎉 Success Stories

This tool has successfully resolved upload speed issues for users experiencing:
- 1 Mbps upload instead of expected 50+ Mbps
- Intermittent network slowdowns
- "Network optimization" software conflicts
- Corporate network policy conflicts

## 📞 Support

- **Issues:** Use GitHub Issues for bug reports
- **Discussions:** Use GitHub Discussions for questions
- **Documentation:** Check this README and script comments

---

**Disclaimer:** This tool modifies Windows services. While designed to be safe, always backup your system and test in a non-production environment first. The authors are not responsible for any system issues that may arise from using this tool.
