---
title: 'Panoramica: autenticazione di servizi di dominio Active Directory locale per le condivisioni file di Azure'
description: Informazioni sull'autenticazione Active Directory Domain Services (AD DS) per le condivisioni file di Azure. Questo articolo descrive gli scenari di supporto, la disponibilità e spiega come funzionano le autorizzazioni tra i servizi di dominio Active Directory e Azure Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/12/2020
ms.author: rogarana
ms.openlocfilehash: 0f2f0def9a23437dff57dda2d1a57950cef86275
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496129"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Panoramica-autenticazione Active Directory Domain Services locale su SMB per le condivisioni file di Azure

[File di Azure](storage-files-introduction.md)   supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite due tipi di servizi di dominio: Azure Active Directory Domain Services (Azure AD DS) e Active Directory Domain Services locale (AD DS). L'uso di un metodo di autenticazione basato su identità consente di integrare il processo di accesso alle condivisioni file di Azure con il processo di autenticazione basato sulle identità esistente, invece di dover gestirlo separatamente. Questa serie di articoli è incentrata sull'abilitazione e la configurazione di servizi di dominio Active Directory locali per l'autenticazione con condivisioni file di Azure.

Se non si ha familiarità con le condivisioni file di Azure, è consigliabile leggere la [Guida alla pianificazione](storage-files-planning.md) prima di leggere la serie di articoli riportata di seguito.

## <a name="supported-scenarios-and-restrictions"></a>Scenari e restrizioni supportati

- Le identità di servizi di dominio Active Directory usate per File di Azure autenticazione di servizi di dominio Active Directory locale devono essere sincronizzate con Azure AD. La sincronizzazione dell'hash delle password è facoltativa. 
- Supporta le condivisioni file di Azure gestite da Sincronizzazione file di Azure.
- Supporta l'autenticazione Kerberos con Active Directory con crittografia RC4-HMAC. La crittografia Kerberos AES non è ancora supportata.
- Supporta Single Sign-On esperienza.
- Supportato solo nei client che eseguono versioni del sistema operativo più recenti di Windows 7 o Windows Server 2008 R2.
- Supportato solo per la foresta di Active Directory in cui è registrato l'account di archiviazione. Per impostazione predefinita, è possibile accedere solo alle condivisioni file di Azure con le credenziali di servizi di dominio Active Directory di una singola foresta. Se è necessario accedere alla condivisione file di Azure da una foresta diversa, verificare che sia configurato il trust tra foreste appropriato. per informazioni dettagliate, vedere le [domande frequenti](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) .
- Non supporta l'autenticazione per gli account computer creati in servizi di dominio Active Directory. 

Quando si abilita Active Directory Domain Services per le condivisioni file di Azure tramite SMB, i computer aggiunti AD DS possono montare condivisioni file di Azure usando le credenziali di Active Directory Domain Services esistenti. Questa funzionalità può essere abilitata con un ambiente di servizi di dominio Active Directory ospitato in computer locali o ospitati in Azure.

> [!NOTE]
> Per semplificare la configurazione dell'autenticazione di Active Directory File di Azure per alcuni casi d'uso comuni, sono stati pubblicati due video con istruzioni dettagliate per gli scenari seguenti:
> - [Sostituzione dei file server locali con File di Azure (inclusa la configurazione del collegamento privato per i file e l'autenticazione di Active Directory)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [Utilizzo di File di Azure come contenitore di profili per desktop virtuale Windows (incluso l'installazione di AD Authentication e la configurazione di FsLogix)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Prerequisiti 

Prima di abilitare l'autenticazione di servizi di dominio Active Directory per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti: 

- Selezionare o creare l' [ambiente di servizi di dominio Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e [sincronizzarlo con Azure ad](../../active-directory/hybrid/how-to-connect-install-roadmap.md) Azure ad Connect. 

    È possibile abilitare la funzionalità in un ambiente AD DS locale nuovo o esistente. Le identità usate per l'accesso devono essere sincronizzate con Azure AD. Il tenant Azure AD e la condivisione file a cui si accede devono essere associati alla stessa sottoscrizione.

- Aggiungere un dominio a un computer locale o una macchina virtuale di Azure in servizi di dominio Active Directory locale. Per informazioni su come aggiungere un dominio, vedere [aggiungere un computer a un dominio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

- Selezionare o creare un account di archiviazione di Azure.  Per ottenere prestazioni ottimali, si consiglia di distribuire l'account di archiviazione nella stessa area del client da cui si prevede di accedere alla condivisione. Quindi, [montare la condivisione file di Azure](storage-how-to-use-files-windows.md) con la chiave dell'account di archiviazione. Il montaggio con la chiave dell'account di archiviazione verifica la connettività.

    Verificare che l'account di archiviazione che contiene le condivisioni file non sia già configurato per l'autenticazione Azure AD DS. Se per l'account di archiviazione è abilitata l'autenticazione File di Azure Azure AD DS, è necessario disabilitarla prima di cambiare per usare servizi di dominio Active Directory locale. Ciò implica che gli ACL esistenti configurati nell'ambiente Azure AD DS dovranno essere riconfigurati per l'imposizione delle autorizzazioni appropriate.

    Se si verificano problemi durante la connessione a File di Azure, vedere [lo strumento per la risoluzione dei problemi pubblicato per file di Azure errori di montaggio in Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Vengono inoltre fornite [indicazioni](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) per aggirare gli scenari in cui la porta 445 è bloccata. 

- Eseguire qualsiasi configurazione di rete pertinente prima di abilitare e configurare l'autenticazione di servizi di dominio Active Directory per le condivisioni file di Azure. Per ulteriori informazioni, vedere [file di Azure considerazioni sulla rete](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Disponibilità a livello di area

File di Azure autenticazione con servizi di dominio Active Directory è disponibile in [tutte le aree pubbliche e gov di Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Panoramica

Se si prevede di abilitare tutte le configurazioni di rete nella condivisione file, è consigliabile leggere l'articolo [considerazioni sulla rete](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) e completare la configurazione correlata prima di abilitare l'autenticazione di servizi di dominio Active Directory.

L'abilitazione dell'autenticazione di servizi di dominio Active Directory per le condivisioni file di Azure consente di autenticare le condivisioni file di Azure con le credenziali di servizi di dominio Active Directory locali. Inoltre, consente di gestire meglio le autorizzazioni per consentire un controllo di accesso granulare. Questa operazione richiede la sincronizzazione delle identità da servizi di dominio Active Directory locali a Azure AD con AD Connect. È possibile controllare l'accesso a livello di condivisione con le identità sincronizzate con Azure AD durante la gestione dell'accesso a livello di file/condivisione con le credenziali di servizi di dominio Active Directory locali.

Quindi, attenersi alla procedura seguente per configurare File di Azure per l'autenticazione di servizi di dominio Active Directory: 

1. [Parte 1: abilitare l'autenticazione di servizi di dominio Active Directory nell'account di archiviazione](storage-files-identity-ad-ds-enable.md)

1. [Parte 2: assegnare le autorizzazioni di accesso per una condivisione all'identità del Azure AD (un utente, un gruppo o un'entità servizio) sincronizzata con l'identità di Active Directory di destinazione](storage-files-identity-ad-ds-assign-permissions.md)

1. [Parte 3: configurare gli ACL di Windows su SMB per directory e file](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Parte 4: montare una condivisione file di Azure in una macchina virtuale aggiunta a servizi di dominio Active Directory](storage-files-identity-ad-ds-mount-file-share.md)

1. [Aggiornare la password dell'identità dell'account di archiviazione in servizi di dominio Active Directory](storage-files-identity-ad-ds-update-password.md)

Il diagramma seguente illustra il flusso di lavoro end-to-end per l'abilitazione dell'autenticazione Azure AD su SMB per le condivisioni file di Azure. 

![Diagramma del flusso di lavoro file AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Le identità usate per accedere alle condivisioni file di Azure devono essere sincronizzate con Azure AD per applicare le autorizzazioni per i file a livello di condivisione tramite il modello di [controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/overview.md) . Gli [elenchi DACL di tipo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) su file/directory trasferiti da file server esistenti verranno conservati e applicati. Questa soluzione offre una facile integrazione con l'ambiente di servizi di dominio Active Directory aziendale. Quando si sostituiscono file server locali con condivisioni file di Azure, gli utenti esistenti possono accedere alle condivisioni file di Azure dai client correnti con un'esperienza Single Sign-On, senza alcuna modifica alle credenziali in uso.  

## <a name="next-steps"></a>Passaggi successivi

Per abilitare l'autenticazione di servizi di dominio Active Directory locale per la condivisione file di Azure, continuare con l'articolo successivo:

[Parte 1: abilitare l'autenticazione di servizi di dominio Active Directory per l'account](storage-files-identity-ad-ds-enable.md)
