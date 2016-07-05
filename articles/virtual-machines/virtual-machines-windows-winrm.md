<properties
	pageTitle="Configurare l'accesso WinRM per le macchine virtuali in Azure Resource Manager | Microsoft Azure"
	description="Come configurare l'accesso WinRM per l'utilizzo con una macchina virtuale Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="singhkay"/>

# Configurare l'accesso WinRM per le macchine virtuali in Azure Resource Manager

## WinRM nella gestione del servizio Azure e Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modello di distribuzione classica

* Per una panoramica di Azure Resource Manager, consultare questo [articolo](../resource-group-overview.md)
* Per conoscere le differenze tra la gestione del servizio Azure e Azure Resource Manager, consultare questo [articolo](../resource-manager-deployment-model.md)

La differenza principale nell'impostazione della configurazione di WinRM tra i due stack consiste nelle modalità di installazione del certificato nella VM. Nello stack di Azure Resource Manager, i certificati vengono modellati come risorse gestite dal provider di risorse dell'insieme di credenziali delle chiavi. Pertanto, l'utente deve fornire il proprio certificato e caricarlo in un insieme di credenziali delle chiavi per poterlo utilizzare in una VM.

Di seguito è descritta la procedura per configurare una VM con connettività WinRM

1. Creare un insieme di credenziali delle chiavi
2. Creare un certificato autofirmato
3. Caricare il certificato autofirmato per l'insieme di credenziali delle chiavi
4. Ottenere l'URL del certificato autofirmato nell'insieme di credenziali delle chiavi
5. Fare riferimento all'URL del certificato autofirmato durante la creazione di una VM

## Passaggio 1: Creare un insieme di credenziali delle chiavi

Il seguente comando consente di creare l'insieme di credenziali delle chiavi

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## Passaggio 2: Creare un certificato autofirmato
Per creare un certificato autofirmato, è possibile utilizzare questo script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName "$certificateName" -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## Passaggio 3: Caricare il certificato autofirmato per l'insieme di credenziali delle chiavi

Prima di caricare il certificato nell'insieme di credenziali delle chiavi creato al Passaggio 1, è necessario convertirlo in un formato comprensibile per il provider di risorse Microsoft.Compute. Per farlo, utilizzare lo script PowerShell qui di seguito

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = get-content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureRmKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## Passaggio 4: Ottenere l'URL del certificato autofirmato nell'insieme di credenziali delle chiavi

Durante il provisioning della VM, il provider di risorse Microsoft.Compute necessita dell'URL della chiave privata all'interno dell'insieme di credenziali delle chiavi. Ciò consente al provider di risorse Microsoft.Compute di scaricare la chiave privata e creare il certificato equivalente nella VM.

>[AZURE.NOTE]L'URL della chiave privata deve includerne anche la versione. Un esempio di URL potrebbe essere il seguente https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### Modelli

Per ottenere il collegamento all'URL nel modello, è possibile utilizzre il codice seguente

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### PowerShell

Questo URL può essere ottenuto utilizzando il seguente comando PowerShell

	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## Passaggio 5: Durante la creazione di una VM, fare riferimento all'URL dei certificati autofirmati

#### Modelli di Azure Resource Manager

Quando si crea una VM tramite modelli, viene fatto riferimento al certificato nelle sezioni delle chiavi private e di WinRM, come indicato di seguito

	"osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Un modello di esempio per quanto detto sopra è disponibile qui: [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Il codice sorgente di questo modello è reperibile in [Github](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### PowerShell

	$vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
	$credential = Get-Credential
	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
	$vm = Set-AzureRmVMOperatingSystem -VM $vm  -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
	$sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
	$CertificateStore = "My"
	$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## Passaggio 6: Connettersi alla VM
Per potersi connettere alla VM è necessario controllare di aver configurato il computer per la gestione remota di WinRM. Avviare PowerShell come amministratore ed eseguire il seguente comando per verificare la configurazione.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Se non si riesce, potrebbe essere necessario verificare che il servizio WinRM sia in esecuzione. Per farlo, utilizzare `Get-Service WinRM`

Dopo avere completato l'installazione, è possibile connettersi alla VM utilizzando il comando seguente

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

<!---HONumber=AcomDC_0622_2016-->