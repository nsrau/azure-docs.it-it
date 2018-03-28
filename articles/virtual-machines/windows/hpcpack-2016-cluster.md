---
title: Cluster HPC Pack 2016 in Azure | Microsoft Docs
description: Informazioni su come distribuire un cluster HPC Pack 2016 in Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: c26dd85d896445e19efb9906d953fd535fc1fb5c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Distribuire un cluster HPC Pack 2016 in Azure

Seguire i passaggi descritti in questo articolo per distribuire un cluster [Microsoft HPC Pack 2016 Update 1](https://technet.microsoft.com/library/cc514029) nelle macchine virtuali di Azure. HPC Pack è la soluzione HPC gratuita di Microsoft basata sulle tecnologie di Microsoft Azure e Windows Server e supporta un'ampia gamma di carichi di lavoro di HPC.

Usare uno dei [modelli di Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) per distribuire il cluster HPC Pack 2016. È possibile scegliere tra diverse topologie di cluster con numeri e tipi differenti di nodi head del cluster e nodi di calcolo.

## <a name="prerequisites"></a>prerequisiti

### <a name="pfx-certificate"></a>Certificato PFX

Un cluster Microsoft HPC Pack 2016 richiede un certificato PFX (scambio di informazioni personali) per rendere sicura la comunicazione tra i nodi HPC. Il certificato deve soddisfare i requisiti seguenti:

* Deve avere una chiave privata abilitata per lo scambio di chiave
* L'uso di chiavi include la firma digitale e la crittografia a chiave
* L'uso di chiavi avanzato include l'autenticazione client e l'autenticazione server

Se si dispone già di un certificato che soddisfa questi requisiti, è possibile richiedere il certificato a un'autorità di certificazione. In alternativa, usare i comandi seguenti per generare i certificati autofirmati in base al sistema operativo in cui si esegue il comando. Esportare quindi il certificato come file PFX protetto da password con la chiave privata.

* **Per Windows 10 o Windows Server 2016**, eseguire il cmdlet integrato di PowerShell **SelfSignedCertificate New** nel modo seguente:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Per i sistemi operativi meno recenti di Windows 10 o Windows Server 2016**, scaricare il [generatore di certificati autofirmati](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarne il contenuto ed eseguire i comandi seguenti al prompt dei comandi di PowerShell:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

Dopo aver creato il certificato nell'archivio Utente corrente, usare lo snap-in Certificati per esportare il certificato come file PFX protetto da password con la chiave privata. È anche possibile esportare il certificato usando il cmdlet di PowerShell [Export-Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps).

### <a name="upload-certificate-to-an-azure-key-vault"></a>Caricare il certificato nell'insieme di credenziali delle chiavi di Azure

Prima di distribuire il cluster HPC, caricare il certificato PFX in un [insieme di credenziali delle chiavi di Azure](../../key-vault/index.md) come segreto e registrare le informazioni seguenti per l'uso durante la distribuzione: **Nome dell'insieme di credenziali**, **Gruppo di risorse dell'insieme di credenziali**, **URL certificato** e **identificazione personale certificato**.

Segue un script di PowerShell di esempio per caricare il certificato, creare l'insieme di credenziali delle chiavi e generare le informazioni necessarie. Per altre informazioni sul caricamento di un certificato in un insieme di credenziali delle chiavi di Azure, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologie supportate

Scegliere uno dei [modelli di Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) per distribuire il cluster HPC Pack 2016. Di seguito vengono descritte le architetture di alto livello di tre topologie di cluster di esempio. Le topologie a disponibilità elevata includono più nodi head del cluster.

1. Il cluster a disponibilità elevata con dominio di Active Directory

    ![Il cluster a disponibilità elevata nel dominio AD](./media/hpcpack-2016-cluster/haad.png)


2. Il cluster a disponibilità elevata senza dominio di Active Directory

    ![Il cluster a disponibilità elevata senza dominio AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Cluster con un singolo nodo head

   ![Cluster con nodo head singolo](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Distribuire un cluster

Per creare il cluster, scegliere un modello e fare clic su **Distribuisci in Azure**. Nel [portale di Azure](https://portal.azure.com) specificare i parametri per il modello come descritto nei passaggi seguenti. Ogni modello crea tutte le risorse di Azure necessarie per l'infrastruttura del cluster HPC. Le risorse sono: rete virtuale di Azure, indirizzo IP pubblico, bilanciamento del carico (solo per un cluster a disponibilità elevata), interfacce di rete, set di disponibilità, account di archiviazione e macchine virtuali.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Passaggio 1: Selezionare la sottoscrizione, il percorso e il gruppo di risorse

La **sottoscrizione** e il **percorso** devono essere gli stessi specificati quando è stato caricato il certificato PFX (vedere la sezione Prerequisiti). È consigliabile creare un **gruppo di risorse** diverso per la distribuzione.

### <a name="step-2-specify-the-parameter-settings"></a>Passaggio 2: Specificare le impostazioni dei parametri

Immettere o modificare i valori dei parametri del modello. Per visualizzare le informazioni della Guida, fare clic sull'icona accanto a ciascun parametro. Vedere anche le linee guida per le [dimensioni delle macchine virtuali disponibili](sizes.md).

Specificare i valori registrati in Prerequisiti per i parametri seguenti: **Nome dell'insieme di credenziali**, **Gruppo di risorse dell'insieme di credenziali**, **URL certificato** e **Identificazione personale certificato**.

### <a name="step-3-review-terms-and-create"></a>Passaggio 3. Esaminare le condizioni e creare
Esaminare i termini e le condizioni associati al modello. Se sono accettabili, fare clic su **Acquista** per avviare la distribuzione.

A seconda della topologia del cluster, la distribuzione può richiedere 30 minuti o più.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster
1. Dopo aver distribuito il cluster HPC Pack, accedere al [portale di Azure](https://portal.azure.com). Fare clic su **Gruppi di risorse** e individuare il gruppo di risorse in cui è stato distribuito il cluster. È possibile cercare le macchine virtuali del nodo head.

    ![Nodi head del cluster nel portale](./media/hpcpack-2016-cluster/clusterhns.png)

2. Fare clic su un nodo head (in un cluster a disponibilità elevata, fare clic su uno qualsiasi dei nodi head). In **Panoramica** è possibile trovare l'indirizzo IP pubblico o il nome DNS completo del cluster.

    ![Impostazioni di connessione del cluster](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Fare clic su **Connetti** per accedere a uno dei nodi head usando Desktop remoto con il nome utente amministratore specificato. Se il cluster distribuito si trova in un dominio di Active Directory, il nome utente sarà nel formato \<NomeDominioPrivato>\\\<NomeUtenteAmministratore> (ad esempio, hpc.local\hpcadmin).

## <a name="next-steps"></a>Passaggi successivi
* Inviare i processi al cluster. Vedere [Inviare i processi a un cluster HPC e HPC Pack in Azure](hpcpack-cluster-submit-jobs.md) e [Gestire un cluster HPC Pack 2016 in Azure usando Azure Active Directory](hpcpack-cluster-active-directory.md).

