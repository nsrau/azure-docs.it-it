---
title: Configurare un'identità comune
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come creare account utente comuni che possono essere usati tra più istanze di Data Science Virtual Machine. È possibile usare Azure Active Directory o un'istanza di Active Directory locale per l'autenticazione degli utenti alla Data Science Virtual Machine.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208151"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Configurare un'identità comune in una macchina virtuale di data scienceSet up a common identity on a Data Science Virtual Machine

In una macchina virtuale di Microsoft Azure, inclusa una macchina data Science (DSVM), è possibile creare account utente locali durante il provisioning della macchina virtuale. Gli utenti eseguono quindi l'autenticazione alla VM usando queste credenziali. Se si dispone di più macchine virtuali a cui gli utenti devono accedere, la gestione delle credenziali può diventare molto complessa. Una soluzione eccellente consiste nel distribuire gli account utente e la gestione comuni tramite un provider di identità basato su standard. Tramite questo approccio, è possibile usare un singolo set di credenziali per accedere a più risorse in Azure, incluse più DSVM.

Active Directory è un provider di identità popolare ed è supportato in Azure sia come servizio cloud che come directory locale. È possibile usare Azure Active Directory (Azure AD) o Active Directory locale per autenticare gli utenti in una DSVM autonoma o in un cluster di DSVM in un set di scalabilità di macchine virtuali. Questo risultato si ottiene aggiungendo le istanze di DSVM a un dominio di Active Directory.

Se si dispone già di Active Directory, è possibile utilizzarlo come provider di identità comune. Se non si dispone di Active Directory, è possibile eseguire un'istanza di Active Directory gestita in Azure tramite Servizi di dominio Azure Active Directory .If you don't have Active Directory, you can run a managed Active Directory instance on Azure through [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

La documentazione per [Azure AD](https://docs.microsoft.com/azure/active-directory/) fornisce istruzioni di [gestione](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)dettagliate, incluse indicazioni sulla connessione di Azure AD alla directory locale, se disponibile.

Questo articolo descrive come configurare un servizio di dominio Active Directory completamente gestito in Azure usando Servizi di dominio Active Directory di Azure.This article describes how to set up a fully managed Active Directory domain service on Azure by using Azure AD DS. È quindi possibile aggiungere le DSVM al dominio active Directory gestito. Questo approccio consente agli utenti di accedere a un pool di DSVM (e altre risorse di Azure) tramite un account utente e credenziali comuni.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurare un dominio di Active Directory completamente gestito in Azure

Active Directory Domain Services semplifica la gestione delle identità, fornendo un servizio completamente gestito in Azure. In questo dominio di Active directory, si gestiscono gli utenti e i gruppi. Per configurare un dominio di Active Directory ospitato da Azure e account utente nella directory, attenersi alla seguente procedura:

1. Nel portale di Azure aggiungere l'utente ad Active Directory: 

   1. Accedere all'interfaccia di amministrazione di [Azure Active Directory](https://aad.portal.azure.com) usando un account amministratore globale per la directory.
    
   1. Selezionare **Azure Active Directory** e quindi **Utenti e gruppi**.
    
   1. In **Utenti e gruppi**selezionare Tutti gli **utenti**e quindi Nuovo **utente**.
   
           The **User** pane opens:
      
      ![Riquadro "Utente"](./media/add-user.png)
    
   1. Immettere dettagli per gli utenti, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "[nome dominio]" del nome di dominio predefinito iniziale o un [nome di dominio personalizzato](../../active-directory/add-custom-domain.md) verificato, non federato, ad esempio "contoso.com".
    
   1. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.
    
   1. Facoltativamente, è possibile aprire e immettere le informazioni in **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. 
    
   1. In **Utente**selezionare **Crea**.
    
   1. Distribuire in modo sicuro la password generata al nuovo utente in modo che possa accedere.

1. Creare un'istanza di Azure Active Directory Domain Services. Seguire le istruzioni in [Abilitare Servizi](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) di dominio Azure Active Directory usando il portale di Azure (sezione "Creare un'istanza e configurare le impostazioni di base"). È importante aggiornare le password utente esistenti in Active Directory in modo che le password in Azure Active Directory Domain Services siano sincronizzate. È anche importante aggiungere DNS ad Azure AD DS, come descritto in "Completare i campi nella finestra Nozioni di base del portale di Azure per creare un'istanza di Servizi di dominio Azure, in tale sezione.

1. Creare una subnet DSVM separata nella rete virtuale creata nella sezione "Creare e configurare la rete virtuale" del passaggio precedente.
1. Creare una o più istanze DSVM nella subnet DSVM.
1. Seguire le [istruzioni](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) per aggiungere DSVM ad Active Directory. 
1. Montare una condivisione File di Azure per ospitare la directory domestica o del blocco appunti in modo che l'area di lavoro possa essere montata in qualsiasi computer. Se sono necessarie autorizzazioni a livello di file rigorose, è necessario disporre di Network File System [NFS] in esecuzione in una o più macchine virtuali.

   1. [Creare una condivisione di File di Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Montare questa condivisione su Linux DSVM. Quando si seleziona Connetti per la condivisione File di Azure nell'account di archiviazione nel portale di Azure, viene visualizzato il comando da eseguire nella shell bash in DSVM Linux.When you select **Connect** for the Azure Files share in your storage account in the Azure portal, the command to run in the bash shell on the Linux DSVM appears. Il comando è simile al seguente:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Si supponga, ad esempio, di aver installato la condivisione File di Azure in /data/workspace. A questo punto, creare le directory per ogni utente nella condivisione: /data/workspace/user1, /data/workspace/user2 e così via. Creare una directory `notebooks` nell'area di lavoro di ogni utente. 
1. Creare collegamenti simbolici per `notebooks` in `$HOME/userx/notebooks/remote`.   

Gli utenti nell'istanza di Active Directory sono ora ospitati in Azure.You now have the users in your Active Directory instance hosted in Azure. Utilizzando le credenziali di Active Directory, gli utenti possono accedere a qualsiasi DSVM (SSH o JupyterHub) aggiunta a Servizi di dominio Active Directory di Azure.By using Active Directory credentials, users can sign in to any DSVM (SSH or JupyterHub) that's joined to Azure AD DS. Poiché l'area di lavoro dell'utente si trova in una condivisione di File di Azure, gli utenti avranno accesso ai propri notebook e ad altro lavoro da qualsiasi DSVM quando usano JupyterHub.

Per la scalabilità automatica è possibile usare un set di scalabilità di macchine virtuali per creare un pool di VM che vengono tutte aggiunte al dominio in questo modo e con il disco condiviso montato. Gli utenti possono accedere a qualsiasi macchina disponibile nel set di scalabilità della macchina virtuale e avere accesso al disco condiviso in cui vengono salvati i blocchi appunti. 

## <a name="next-steps"></a>Passaggi successivi

* [Archiviare le credenziali in modo sicuro per accedere alle risorse cloud](dsvm-secure-access-keys.md)



