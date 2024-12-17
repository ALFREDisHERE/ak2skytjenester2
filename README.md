# ak2skytjenester2 er her

Terraform-prosjekt oppretter 1 VM med webtjeneste og 2 VM med database i lastbalansering

## Struktur
# Terraform Project Structure

```plaintext
terraform-project/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── provider.tf
└── modules/
    ├── network/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    ├── vm/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    ├── loadbalancer/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    └── db-install/
        ├── main.tf
        ├── variables.tf
        ├── outputs.tf
        └── scripts/
            ├── install_mariadb.sh
            └── playbook.yml
```
## Instruksjoner for oppsett

### Forutsetninger

- Installert [Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli). <-- anbefales!
- Azure-konto med nødvendige rettigheter.
- Installert [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli) og autentisert med `az login`.
- Installert [GIT](https://github.com/git-guides/install-git)

### Nedlastning og oppsett

1. **NEDLASTNING**:
- åpne mappen du ønsker å ha filer i, høyreklikk mens du holder nede CTRL og shift. velg åpne powershell her (eventuelt cd <directory>)
- skriv git clone url-til-repo
```powershell
git clone https://github.com/ALFREDisHERE/ak2skytjenester2
```
2. **ENDRINGER**
- Gjør nødvendige endringer i **`variables.tfvars`**
- Endringer som må gjøres manuelt:
  - Linje 6 og 7 i **`modules/db-install/scripts/install_mariadb.sh`** må ha lik verdi som `admin_username` og `admin_password` i **`terraform.tfvars`**
  - Linje 64 og 65 i **`modules/db-install/scripts/playbook.yml`** må ha lik verdi som `admin_username` og `admin_password` i **`terraform.tfvars`**
3. **KJØRE TERRAFORM VIA POWERSHELL**
- Det kan hende at man ikke klarer å logge inn med `az login`, men da følger man instruksjonene som dukker opp i Powershell (have fun).
  ```powershell
  cd <stien til Terraform-mappen>
  az login
  terraform init
  terraform plan -out main.tfplan
  terraform apply main.tfplan
  ```
- Det kan hende at man får en feilmelding relatert til MariaDB installasjon, men da kan man slette ressursene i Azure og prøve å kjøre Terraform på nytt. Begynn på terraform plan steget.
4. **BENYTTE PROSJEKTET**
- Skriv den offentlige IP-adressen som står i `web_vm_public_ip = "<IP-adresse>"` som dukker opp i outputs i en nettleser for å få vise nettsiden som har kontakt med databasen.
  - Det kan hende at det tar litt tid før nettsiden som henter data fra databasen er ferdig satt opp. Der vil du se noen navn dukke opp som bekrefter at nettsiden har tilgang til databasen
- Skriv `terraform output admin_password` for å vise passordet til adminbrukeren.
- For å koble til webserveren via SSH:
  ```powershell
  ssh <admin_username>@<offentlige IP-adressen>
  <admin_username>@<offentlige IP-adressen> password: <admin_password>
  ```
- For å koble til DB-serverne via SSH fra webserver:
  ```powershell
  ssh <admin_username>@<IP-adressen til VM>
  <admin_username>@<IP-adressen til VM> password: <admin_password>
  ```
5. **ELIMINERE RESSURSER ETTER OPPSETT**
- Plan og apply destroy for prosjektet:
  ```powershell
  terraform plan -destroy -out main.destroy.tfplan
  terraform apply main.destroy.tfplan
  ```
