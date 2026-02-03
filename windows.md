# Windows Port & Process Management

Quick reference for finding, inspecting, and killing processes by port on Windows.

---

## Find All Listening Ports

```cmd
netstat -ano | findstr LISTENING
```

| Flag | What it does |
|------|-------------|
| `-a` | Show all connections and listening ports |
| `-n` | Show addresses and ports as numbers (skip DNS lookup) |
| `-o` | Show the owning process ID (PID) |

---

## Find a Specific Port

```cmd
netstat -ano | findstr :<PORT>
```

**Examples:**

```cmd
netstat -ano | findstr :3000
netstat -ano | findstr :8080
netstat -ano | findstr :5432
```

> [!TIP]
> Include the colon (`:`) before the port number to avoid partial matches.
> Without it, searching for `80` would also match `8080`, `8081`, etc.

---

## Identify the Process Using a Port

Once you have the PID from `netstat`, look it up:

```cmd
tasklist /FI "PID eq <PID>"
```

**Example:**

```cmd
tasklist /FI "PID eq 12345"
```

---

## Kill a Process by PID

```cmd
taskkill /PID <PID> /F
```

| Flag | What it does |
|------|-------------|
| `/PID` | Target a specific process ID |
| `/F` | Force kill (don't ask nicely) |

> [!WARNING]
> `/F` terminates the process immediately with no graceful shutdown.
> For services like databases, try without `/F` first.

---

## Kill a Process by Name

```cmd
taskkill /IM <process_name> /F
```

**Example:**

```cmd
taskkill /IM node.exe /F
```

> [!CAUTION]
> This kills **all** instances of that process. If you have multiple Node
> servers running, they all die. Use `/PID` to target a specific one.

---

## One-Liner: Find and Display Port Usage (PowerShell)

```powershell
Get-NetTCPConnection -State Listen | 
  Select-Object LocalPort, OwningProcess, 
    @{Name="Process";Expression={(Get-Process -Id $_.OwningProcess).ProcessName}} | 
  Sort-Object LocalPort | 
  Format-Table -AutoSize
```

This gives you a clean table with port, PID, and process name all in one shot.

---

## Common Ports Reference

| Port | Typical Use |
|------|------------|
| `80` | HTTP |
| `443` | HTTPS |
| `3000` | Node.js / React dev server |
| `3306` | MySQL |
| `5173` | Vite dev server |
| `5432` | PostgreSQL |
| `8000` | Python / FastAPI |
| `8080` | General dev / proxies |
| `8443` | HTTPS alt |
| `9090` | Various admin panels |
