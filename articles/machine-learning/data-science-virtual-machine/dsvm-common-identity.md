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
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208151"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Configurare un'identità comune in un Data Science Virtual Machine

In una macchina virtuale Microsoft Azure (VM), incluso un Data Science Virtual Machine (DSVM), è possibile creare account utente locali durante il provisioning della macchina virtuale. Gli utenti eseguono quindi l'autenticazione alla VM usando queste credenziali. Se sono presenti più macchine virtuali a cui gli utenti devono accedere, la gestione delle credenziali può essere molto complessa. Un'ottima soluzione consiste nel distribuire gli account utente e la gestione comuni tramite un provider di identità basato su standard. Con questo approccio, è possibile usare un singolo set di credenziali per accedere a più risorse in Azure, tra cui più Dsvm.

Active Directory è un provider di identità noto ed è supportato in Azure sia come servizio cloud che come directory locale. È possibile usare Azure Active Directory (Azure AD) o Active Directory locale per autenticare gli utenti in una DSVM autonoma o in un cluster di DSVM in un set di scalabilità di macchine virtuali. Questo risultato si ottiene aggiungendo le istanze di DSVM a un dominio di Active Directory.

Se si dispone già di Active Directory, è possibile usarlo come provider di identità comune. Se non si dispone di Active Directory, è possibile eseguire un'istanza di Active Directory gestita in Azure tramite [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

La documentazione per [Azure ad](https://docs.microsoft.com/azure/active-directory/) fornisce [istruzioni di gestione](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)dettagliate, incluse le indicazioni sulla connessione Azure ad alla directory locale, se disponibile.

Questo articolo descrive come configurare un servizio del dominio Active Directory completamente gestito in Azure usando Azure AD DS. È quindi possibile aggiungere il Dsvm al dominio gestito Active Directory. Questo approccio consente agli utenti di accedere a un pool di Dsvm (e ad altre risorse di Azure) tramite un account utente comune e le credenziali.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurare un dominio di Active Directory completamente gestito in Azure

Active Directory Domain Services semplifica la gestione delle identità, fornendo un servizio completamente gestito in Azure. In questo dominio di Active directory, si gestiscono gli utenti e i gruppi. Per configurare un dominio Active Directory host di Azure e gli account utente nella directory, seguire questa procedura:

1. Nel portale di Azure aggiungere l'utente ad Active Directory: 

   1. Accedere al centro di [amministrazione di Azure Active Directory](https://aad.portal.azure.com) usando un account amministratore globale per la directory.
    
   1. Selezionare **Azure Active Directory** e quindi **Utenti e gruppi**.
    
   1. In **utenti e gruppi**selezionare **tutti gli utenti**e quindi selezionare **nuovo utente**.
   
           The **User** pane opens:
      
      ![Riquadro "Utente"](./media/add-user.png)
    
   1. Immettere dettagli per gli utenti, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "[nome dominio]" del nome di dominio predefinito iniziale o un [nome di dominio personalizzato](../../active-directory/add-custom-domain.md) verificato, non federato, ad esempio "contoso.com".
    
   1. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.
    
   1. Facoltativamente, è possibile aprire e immettere le informazioni in **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. 
    
   1. In **utente**selezionare **Crea**.
    
   1. Distribuire in modo sicuro la password generata al nuovo utente per poter accedere.

1. Creare un'istanza di Azure Active Directory Domain Services. Seguire le istruzioni riportate in [abilitare Azure Active Directory Domain Services usando il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (la sezione "creare un'istanza e configurare le impostazioni di base"). È importante aggiornare le password utente esistenti in Active Directory in modo che le password in Azure Active Directory Domain Services siano sincronizzate. È anche importante aggiungere DNS a Azure AD DS, come descritto in "completare i campi nella finestra nozioni di base della portale di Azure per creare un'istanza di Azure AD DS" in questa sezione.

1. Creare una subnet DSVM separata nella rete virtuale creata nella sezione "creare e configurare la rete virtuale" del passaggio precedente.
1. Creare una o più istanze di DSVM nella subnet DSVM.
1. Seguire le [istruzioni](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) per aggiungere DSVM a Active Directory. 
1. Montare una condivisione File di Azure per ospitare la directory Home o notebook, in modo che sia possibile montare l'area di lavoro in qualsiasi computer. Se sono necessarie autorizzazioni strette a livello di file, è necessario che il file System di rete [NFS] sia in esecuzione in una o più macchine virtuali.

   1. [Creare una condivisione di File di Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Montare questa condivisione nella DSVM Linux. Quando si seleziona **Connetti** per la condivisione file di Azure nell'account di archiviazione nell'portale di Azure, viene visualizzato il comando da eseguire nella shell bash sul DSVM Linux. Il comando è simile al seguente:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Si supponga, ad esempio, di aver montato la condivisione File di Azure in/data/Workspace. A questo punto, creare le directory per ogni utente nella condivisione:/data/Workspace/User1,/data/Workspace/User2 e così via. Creare una directory `notebooks` nell'area di lavoro di ogni utente. 
1. Creare collegamenti simbolici per `notebooks` in `$HOME/userx/notebooks/remote`.   

Sono ora disponibili gli utenti nell'istanza di Active Directory ospitata in Azure. Usando le credenziali Active Directory, gli utenti possono accedere a qualsiasi DSVM (SSH o JupyterHub) aggiunto a Azure AD DS. Poiché l'area di lavoro dell'utente si trova in una condivisione di File di Azure, gli utenti avranno accesso ai propri notebook e ad altro lavoro da qualsiasi DSVM quando usano JupyterHub.

Per la scalabilità automatica è possibile usare un set di scalabilità di macchine virtuali per creare un pool di VM che vengono tutte aggiunte al dominio in questo modo e con il disco condiviso montato. Gli utenti possono accedere a qualsiasi computer disponibile nel set di scalabilità di macchine virtuali e avere accesso al disco condiviso in cui vengono salvati i notebook. 

## <a name="next-steps"></a>Passaggi successivi

* [Archiviare le credenziali in modo sicuro per accedere alle risorse cloud](dsvm-secure-access-keys.md)



