---
title: Configurare l'identità comune per la macchina virtuale di data science - Azure | Microsoft Docs
description: Configurare l'identità comune negli ambienti di macchine virtuali di data science del team aziendale.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 431d3079c0d942c15ccbc8352261ccfe1f5f6e47
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Configurare l'identità comune nella macchina virtuale di data science

Per impostazione predefinita, nelle macchine virtuali di Azure (inclusa la macchina virtuale di data science) gli account utente locali vengono creati durante il provisioning della macchina virtuale e gli utenti eseguono l'autenticazione per la macchina virtuale con queste credenziali. Se sono disponibili più macchine virtuali a cui è necessario accedere, questo approccio può diventare rapidamente oneroso per la gestione delle credenziali. La gestione e gli account utente comuni che usano un provider di identità basato su standard consentono di usare un singolo set di credenziali per accedere a più risorse in Azure, incluse più macchine virtuali di data science. 

Active Directory (AD) è un provider di identità molto diffuso ed è supportato sia in Azure come servizio che in locale. È possibile usare AD o Azure AD per autenticare gli utenti sia in una macchina virtuale di data Science (DSVM) autonoma che in un cluster di DSVM in un set di scalabilità di macchine virtuali Azure. Questa operazione viene eseguita aggiungendo le istanze delle macchine virtuali di data science a un dominio di AD. Se è già disponibile un'istanza di Active Directory per gestire le identità, è possibile usarla come provider di identità comune. Nel caso in cui non sia disponibile un'istanza di AD, è possibile eseguire un'istanza gestita di AD in Azure tramite un servizio denominato [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/). 

La documentazione relativa ad [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) fornisce [istruzioni](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) dettagliate per l'istanza gestita di Active Directory, inclusa la connessione dell'istanza di Azure AD alla directory locale, se presente. 

Nella parte restante di questo articolo vengono descritti i passaggi per la configurazione di un servizio di dominio AD completamente gestito in Azure usando Azure Active Directory Domain Services e per l'aggiunta delle macchine virtuali di data science al dominio AD gestito per consentire agli utenti di accedere a un pool di macchine virtuali di data science e ad altre risorse di Azure tramite un account utente e credenziali comuni. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configurare un dominio di Active Directory completamente gestito in Azure

Active Directory Domain Services semplifica la gestione delle identità, fornendo un servizio completamente gestito in Azure. In questo dominio di Active directory, gli utenti e i gruppi sono gestiti.  Seguire questa procedura per configurare un dominio di AD ospitato in Azure e gli account utente nella directory:

1. Aggiungere utenti in Active Directory nel portale 

    a. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con un account di amministratore globale per la directory del tenant.
    
    b. Selezionare **Azure Active Directory** e quindi **Utenti e gruppi**.
    
    c. In **Utenti e gruppi** selezionare **Tutti gli utenti** e quindi **Nuovo utente**.
   
   ![Selezione del comando Aggiungi](./media/add-user.png)
    
    d. Immettere dettagli per gli utenti, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "[nome dominio]" del nome di dominio predefinito iniziale o un nome di dominio verificato, non federato, [personalizzato, ](../../active-directory/add-custom-domain.md)ad esempio "contoso.com".
    
    e. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.
    
    f. Facoltativamente, è possibile aprire e immettere le informazioni in **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. 
    
    g. In **Utente** selezionare **Crea**.
    
    h. Distribuire in modo sicuro la password generata al nuovo utente per consentirgli l'accesso.

2.  Creare un'istanza di Azure AD Domain Services

    Per creare un'istanza di Azure AD Domain Services, seguire le istruzioni nell'articolo "[Abilitare Azure Active Directory Domain Services tramite il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)" (dall'attività 1 all'attività 5). È importante che le password utente esistenti in Active Directory vengano aggiornate in modo che le password in Azure Active Directory Domain Services siano sincronizzate. È anche importante aggiungere il DNS ad Azure Active Directory Domain Services come indicato nell'attività 4 dell'articolo precedente. 

3.  Creare una subnet separata di macchine virtuali di data science nella rete virtuale creata nell'attività 2 del passaggio precedente
4.  Creare una o più istanze di macchina virtuale di data science nella subnet delle macchine virtuali di data science 
5.  Seguire le [istruzioni](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) per aggiungere macchine virtuali di data science ad Active Directory. 
6.  Montare quindi un'istanza condivisa di File di Azure per ospitare la directory home o notebook e abilitare il montaggio dell'area di lavoro in qualsiasi macchina. Se sono necessarie autorizzazioni limitate a livello di file, è necessario un NFS in esecuzione in una o più macchine virtuali.

    a. [Creare una condivisione file di Azure](../../storage/files/storage-how-to-create-file-share.md)
    
    b. Montarla nella macchina virtuale di data scienceLinux. Quando si fa clic sul pulsante "Connetti" per File di Azure nel proprio account di archiviazione nel portale Azure, verrà visualizzato il comando per l'esecuzione nella shell di Bash nella macchina virtuale di data science Linux. Il comando avrà un aspetto simile al seguente:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Si supponga di aver montato File di Azure in /data/workspace. Creare ora le directory per ognuno degli utenti della condivisione. /data/workspace/user1, /data/workspace/user2 e così via. Creare una directory ```notebooks``` nell'area di lavoro di ogni utente. 
8. Creare collegamenti simbolici per i ```notebooks``` in ```$HOME/userx/notebooks/remote```.   

Gli utenti si trovano ora nella propria istanza di Active Directory ospitata in Azure e possono accedere a qualsiasi macchina virtuale di data science (sia SSH che Jupyterhub) aggiunta ad Azure Active Directory Domain Services usando le credenziali AD. Poiché l'area di lavoro dell'utente si trova in un'istanza condivisa di File di Azure, l'utente avrà accesso ai propri notebook e ad altro lavoro da qualsiasi macchina virtuale di data science quando si usa Jupyterhub. 

Per la scalabilità automatica è possibile usare il set di scalabilità di macchine virtuali per creare un pool di VM che vengono tutte aggiunte al dominio in questo modo e con il disco condiviso montato. Gli utenti possono accedere a qualsiasi macchina disponibile nel set di scalabilità di macchine virtuali e avere accesso al disco condiviso in cui sono salvati i propri notebook. 

## <a name="next-steps"></a>Passaggi successivi

* [Archiviare le credenziali in modo sicuro per accedere alle risorse cloud](dsvm-secure-access-keys.md)



