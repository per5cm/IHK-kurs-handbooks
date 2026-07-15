# EF Core on Fedora Linux
> Short Manual: Docker + SQL server container + EF Core migration on Fedora Linux

---
## 1. Docker Desktop

````bash
sudo dnf install ~/docker-desktop-x86_64.rpm
````

**If Fail**
````bash
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager addrepo --from-repofile=https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install docker-ce-cli
````
**after run installation command again.**

---
## 2. Docker Desktop Sign-in

**If you want to sign in:**

```bash
which pass
gpg --list-secret-keys --keyid-format LONG
```

If you have no GPG-Key:

```bash
gpg --full-generate-key
```
(RSA and RSA, 4096 Bit, no expiration, Name + E-Mail + pass phrase)

```bash
pass init "deine-email@example.com"
```

Docker Desktop restart:
```bash
pkill -f docker-desktop
```

---

## 3. `dotnet-ef` Tool installation & PATH fix

```bash
dotnet tool install --global dotnet-ef
```

if `dotnet ef` after "command not found" comes out:

```bash
echo $PATH | grep dotnet/tools
```

if empty:
```bash
echo 'export PATH="$PATH:$HOME/.dotnet/tools"' >> ~/.bashrc
source ~/.bashrc
```

Verification:
```bash
ls ~/.dotnet/tools        
dotnet ef --version        
```

if `source` nothing happens: close the terminal and open again.

---

## 4. SQL Server Container start

`compose.yaml` in Project-Root:

```yaml
services:
  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=YourStrong!Passw0rd
    ports:
      - "1433:1433"
```

Start:

```bash
docker compose up -d db
docker ps    
```

**Wichtig:** Wait a bit till server goes up.

---

## 5. Connection String 

`appsettings.json`:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost,1433;Database=AlphaQuiz;User Id=sa;Password=YourStrong!Passw0rd;TrustServerCertificate=True"
  }
}
```

**Zwei Stolperfallen:**

- `ConnectionStrings` muss auf **oberster Ebene** stehen, nicht verschachtelt in `Logging` oder einem anderen Block.
- Das Passwort hier muss **exakt** mit `SA_PASSWORD` (oder `MSSQL_SA_PASSWORD`) in `compose.yaml` übereinstimmen — sonst „Login failed for user 'sa'".

---

## 6. Migration 

```bash
dotnet ef migrations add InitialCreate -p <Infrastructure-Projekt> -s <Web-Projekt>
dotnet ef database update -p <Infrastructure-Projekt> -s <Web-Projekt>
```

- `-p` = Projekt mit dem `DbContext`
- `-s` = Startup-Projekt (liest Konfiguration/Connection String von dort)

---