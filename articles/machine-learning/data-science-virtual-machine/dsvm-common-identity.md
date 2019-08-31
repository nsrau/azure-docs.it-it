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
ms.openlocfilehash: 5cd310dac28b999af3d21c46b108abdc6d5779b5
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192245"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Configurare un'identità comune in Data Science Virtual Machine

In una macchina virtuale (VM) di Azure, inclusa Data Science Virtual Machine (DSVM), si creano gli account utente locali durante il provisioning della VM. Gli utenti eseguono quindi l'autenticazione alla VM usando queste credenziali. Se sono disponibili più macchine virtuali a cui è necessario accedere, questo approccio può diventare rapidamente oneroso per la gestione delle credenziali. La gestione e gli account utente comuni tramite un provider di identità basato su standard consentono di usare un singolo set di credenziali per accedere a più risorse in Azure, incluse più macchine virtuali di data science. 

Active Directory è un provider di identità molto diffuso ed è supportato in Azure come servizio e in locale. È possibile usare Azure Active Directory (Azure AD) o Active Directory locale per autenticare gli utenti in una DSVM autonoma o in un cluster di DSVM in un set di scalabilità di macchine virtuali. Questo risultato si ottiene aggiungendo le istanze di DSVM a un dominio di Active Directory. 

Se è già disponibile Active Directory per gestire le identità, è possibile usarlo come provider di identità comune. Se Active Directory non è disponibile, è possibile eseguire un'istanza gestita di Active Directory in Azure tramite un servizio denominato [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/). 

La documentazione relativa ad [Azure AD](https://docs.microsoft.com/azure/active-directory/) fornisce [istruzioni di gestione](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution) dettagliate per l'istanza gestita di Active Directory, inclusa la connessione dell'istanza di Azure AD alla directory locale, se presente. 

Questo articolo illustra la procedura di configurazione di un servizio di dominio di Active Directory completamente gestito in Azure tramite Azure Active Directory Domain Services. È quindi possibile aggiungere le DSVM al dominio di Active Directory gestito per consentire agli utenti di accedere a un pool di DSVM (e altre risorse di Azure) usando un account utente e credenziali comuni. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurare un dominio di Active Directory completamente gestito in Azure

Active Directory Domain Services semplifica la gestione delle identità, fornendo un servizio completamente gestito in Azure. In questo dominio di Active directory, si gestiscono gli utenti e i gruppi. Seguire questa procedura per configurare un dominio di Active Directory ospitato in Azure e gli account utente nella directory:

1. Nel portale di Azure aggiungere l'utente ad Active Directory: 

   a. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con un account di amministratore globale per la directory del tenant.
    
   b. Selezionare **Azure Active Directory** e quindi **Utenti e gruppi**.
    
   c. In **Utenti e gruppi** selezionare **Tutti gli utenti** e quindi **Nuovo utente**.
   
      Si apre il riquadro **Utente**.
      
      ![Riquadro "Utente"](./media/add-user.png)
    
   d. Immettere dettagli per gli utenti, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "[nome dominio]" del nome di dominio predefinito iniziale o un [nome di dominio personalizzato](../../active-directory/add-custom-domain.md) verificato, non federato, ad esempio "contoso.com".
    
   e. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.
    
   f. Facoltativamente, è possibile aprire e immettere le informazioni in **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. 
    
   g. In **Utente** selezionare **Crea**.
    
   h. Distribuire in modo sicuro la password generata al nuovo utente per consentirgli l'accesso.

1. Creare un'istanza di Azure Active Directory Domain Services. Seguire le istruzioni nell'articolo [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (dall'attività 1 all'attività 5). È importante aggiornare le password utente esistenti in Active Directory in modo che le password in Azure Active Directory Domain Services siano sincronizzate. È anche importante aggiungere il DNS ad Azure Active Directory Domain Services come indicato nell'attività 4 dell'articolo. 

1. Creare una subnet separata di DSVM nella rete virtuale creata nell'attività 2 del passaggio precedente.
1. Creare una o più istanze di Data Science VM nella subnet delle DSVM. 
1. Seguire le [istruzioni](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) per aggiungere la DSVM ad Active Directory. 
1. Montare una condivisione di File di Azure per ospitare la directory home o notebook e abilitare il montaggio dell'area di lavoro in qualsiasi macchina. Se sono necessarie autorizzazioni limitate a livello di file, sarà necessario un NFS in esecuzione in una o più macchine virtuali.

   a. [Creare una condivisione di File di Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Montarla nella macchina virtuale di data scienceLinux. Quando si seleziona il pulsante **Connetti** per la condivisione di File di Azure nel proprio account di archiviazione nel portale di Azure, viene visualizzato il comando per l'esecuzione nella shell di Bash nella DSVM Linux. Il comando è simile al seguente:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Si supponga, ad esempio, di avere montato la condivisione di File di Azure in /data/workspace. Creare ora le directory per ogni utente nella condivisione: /data/workspace/user1, /data/workspace/user2 e così via. Creare una directory `notebooks` nell'area di lavoro di ogni utente. 
1. Creare collegamenti simbolici per `notebooks` in `$HOME/userx/notebooks/remote`.   

Gli utenti nell'istanza di Active Directory sono ora ospitati in Azure. Usando le credenziali di Active Directory, gli utenti possono accedere a qualsiasi DSVM (SSH o JupyterHub) aggiunta ad Azure Active Directory Domain Services. Poiché l'area di lavoro dell'utente si trova in una condivisione di File di Azure, gli utenti avranno accesso ai propri notebook e ad altro lavoro da qualsiasi DSVM quando usano JupyterHub. 

Per la scalabilità automatica è possibile usare un set di scalabilità di macchine virtuali per creare un pool di VM che vengono tutte aggiunte al dominio in questo modo e con il disco condiviso montato. Gli utenti possono accedere a qualsiasi computer disponibile nel set di scalabilità di macchine virtuali e avere accesso al disco condiviso in cui sono salvati i propri notebook. 

## <a name="next-steps"></a>Passaggi successivi

* [Archiviare le credenziali in modo sicuro per accedere alle risorse cloud](dsvm-secure-access-keys.md)



