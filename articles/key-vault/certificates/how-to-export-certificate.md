---
title: Esportare un certificato da Azure Key Vault
description: Esportare un certificato da Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588893"
---
# <a name="export-certificate-from-azure-key-vault"></a>Esportare un certificato da Azure Key Vault

Azure Key Vault consente di effettuare facilmente il provisioning, la gestione e la distribuzione di certificati digitali per la rete e di abilitare comunicazioni sicure per le applicazioni. Per informazioni più generali sui certificati, vedere [Certificati di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>Informazioni sui certificati di Azure Key Vault

### <a name="composition-of-certificate"></a>Componenti di un certificato
Quando viene creato un certificato Key Vault, vengono creati anche una chiave e un segreto indirizzabili con lo stesso nome. La chiave Key Vault consente operazioni di chiave e il segreto Key Vault consente il recupero del valore del certificato come segreto. Un certificato Key Vault contiene inoltre metadati del certificato x509 pubblico. [Altre informazioni](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Chiavi esportabili o non esportabili
Un certificato di Key Vault creato può essere recuperato dal segreto indirizzabile con la chiave privata in formato PFX o PEM. I criteri usati per creare il certificato devono indicare che la chiave è esportabile. Se i criteri indicano che non è esportabile, la chiave privata non farà parte del valore recuperato come segreto.

Sono supportati due tipi di chiave RSA oppure RSA HSM con certificati. La possibilità di essere esportabile è consentita solo con RSA, non è supportata da RSA HSM. [Altre informazioni](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Un certificato archiviato in Azure Key Vault può essere esportato tramite l'interfaccia della riga di comando di Azure, PowerShell o il portale.

> [!NOTE]
> È importante notare che la password di un certificato è necessaria solo durante l'importazione nell'insieme di credenziali delle chiavi. Key Vault non salva la password associata, quindi quando si esporta il certificato la password sarà vuota.

## <a name="exporting-certificate-using-cli"></a>Esportazione del certificato tramite l'interfaccia della riga di comando
Il comando seguente consentirà di scaricare la **parte pubblica** di un certificato di Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Per esempi e definizioni dei parametri, [vedere qui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Se si vuole scaricare l'intero certificato, ovvero **la parte pubblica e privata dei rispettivi componenti**, scaricare il certificato come segreto.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
Per le definizioni dei parametri, [vedere qui](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Esportazione del certificato tramite PowerShell

Questo comando ottiene il certificato denominato TestCert01 dall'insieme di credenziali delle chiavi denominato ContosoKV01. Per scaricare il certificato come file PFX, eseguire questo comando. Questi comandi accedono a SecretId e quindi salvano il contenuto come file PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Verrà esportata l'intera catena di certificati con chiave privata e questo certificato sarà protetto da password.
Per altre informazioni sul comando ```Get-AzKeyVaultCertificate``` e sui parametri, vedere l'[Esempio 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>Esportazione del certificato tramite il portale

Quando si crea/importa un certificato nel pannello Certificato del portale, si riceverà la notifica del completamento della creazione del certificato. Quando si seleziona il certificato, è possibile fare clic sulla versione corrente e verrà visualizzata l'opzione per il download.


Fare clic sul pulsante "Scarica in formato CER" o "Scarica in formato PFX/PEM" per scaricare il certificato.


![Download del certificato](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Esportazione del certificato del servizio app da Key Vault

I certificati del servizio app di Azure consentono di acquistare con facilità i certificati SSL e assegnarli alle app Azure direttamente nel portale. Questi certificati possono essere anche esportati dal portale come file PFX per essere usati altrove.
Dopo l'importazione i certificati del servizio app possono essere **inclusi nei segreti**.

Per la procedura di importazione dei certificati del servizio app, [leggere qui](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>Altre informazioni
* [Tipi di file e definizioni di diversi certificati](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)