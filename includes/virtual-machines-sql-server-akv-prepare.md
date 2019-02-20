---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: dfcd9e017675d6ab8799d137b8ac985434a218ba
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213071"
---
## <a name="prepare-for-akv-integration"></a>Preparare l'integrazione di AKV
Per usare l'integrazione dell'insieme di credenziali delle chiavi di Azure per configurare la macchina virtuale di SQL Server, sono necessari diversi prerequisiti: 

1. [Installare Azure PowerShell](#install)
2. [Creare un'istanza di Azure Active Directory](#register)
3. [Creare un insieme di credenziali delle chiavi](#createkeyvault)

Le sezioni seguenti descrivono tali prerequisiti e le informazioni da raccogliere per eseguire i cmdlet di PowerShell in un secondo momento.

### <a id="install"></a> Installare Azure PowerShell
Assicurarsi che sia installata la versione più recente di Azure PowerShell SDK. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Registrare un'applicazione in Azure Active Directory

Innanzitutto, è necessario che un'istanza di [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) sia presente nella sottoscrizione. AAD offre numerosi vantaggi, ad esempio, consente di concedere l'autorizzazione all'insieme di credenziali delle chiavi per determinati utenti e applicazioni.

Successivamente, registrare un'applicazione con AAD. In questo modo, è disponibile per l'utente un account dell'entità servizio con accesso all'insieme di credenziali delle chiavi necessario per la macchina virtuale. Nell'articolo sull'insieme di credenziali delle chiavi di Azure è possibile trovare questi passaggi nella sezione [Registrare un'applicazione con Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory). In alternativa, è possibile vedere i passaggi con gli screenshot nella sezione **Get an identity for the application** (Ottenere un'identità per l'applicazione) di [questo post di blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Prima di completare questi passaggi, è necessario raccogliere le informazioni seguenti durante la registrazione, utili in seguito quando si abilita l'integrazione di Azure Key Vault nella macchina virtuale di SQL.

* Dopo aver aggiunto l'applicazione, trovare l'**ID applicazione** nel pannello **App registrata**.
    L'ID applicazione viene assegnato successivamente al parametro **$spName** (nome entità servizio) nello script di PowerShell per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

   ![ID applicazione](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Quando si crea la chiave durante la procedura, copiare il segreto della chiave come illustrato nello screenshot seguente. Tale segreto della chiave viene assegnato successivamente al parametro **$spSecret** (segreto entità servizio) nello script di PowerShell.

   ![Segreto AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* L'ID applicazione e il segreto verranno usati anche per creare le credenziali in SQL Server.

* È necessario autorizzare questo nuovo ID client per disporre delle autorizzazioni di accesso seguenti: **get**, **wrapKey**, **unwrapKey**. Questa operazione viene eseguita con il cmdlet [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy). Per altre informazioni, vedere [Panoramica di Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a id="createkeyvault"></a> Creare un insieme di credenziali delle chiavi
Per usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi da usare per la crittografia nella macchina virtuale, è necessario accedere a un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi non è ancora stato configurato, crearne uno seguendo i passaggi nell'articolo [Introduzione ad Azure Key Vault](../articles/key-vault/key-vault-overview.md). Prima di completare questi passaggi, è necessario raccogliere alcune informazioni durante l'impostazione, utili in seguito quando si abilita l'integrazione di Azure Key Vault nella macchina virtuale di SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Al passaggio Creare un insieme di credenziali delle chiavi, si noti la proprietà **vaultUri** restituita, ovvero l'URL dell'insieme di credenziali delle chiavi. Nell'esempio fornito in questo passaggio, riportato di seguito, il nome dell'insieme di credenziali delle chiavi è ContosoKeyVault, quindi l'URL dell'insieme di credenziali delle chiavi è https://contosokeyvault.vault.azure.net/.

L'URL dell'insieme di credenziali delle chiavi viene assegnato successivamente al parametro **$akvURL** nello script di PowerShell, per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

Dopo aver creato l'insieme di credenziali delle chiavi, è necessario aggiungere una chiave all'insieme di credenziali delle chiavi. Questa chiave verrà usata successivamente come riferimento durante la creazione di una chiave asimmetrica in SQL Server.
