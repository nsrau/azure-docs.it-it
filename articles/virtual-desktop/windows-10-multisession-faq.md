---
title: Domande frequenti su più sessioni di Windows 10 Enterprise - Azure
description: Domande frequenti e procedure consigliate per l'uso di Windows 10 Enterprise multisessione per Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637090"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Domande frequenti su Windows 10 Enterprise multisessione

Questo articolo risponde alle domande frequenti e spiega le procedure consigliate per la sessione multisessione di Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Che cos'è una sessione multipla di Windows 10 Enterprise?

Windows 10 Enterprise multi-sessione, precedentemente noto come Windows 10 Enterprise per desktop virtuali (EVD), è un nuovo Host sessione Desktop remoto che consente più sessioni interattive simultanee. In precedenza, solo Windows Server poteva eseguire questa operazione. Questa funzionalità offre agli utenti un'esperienza familiare di Windows 10, mentre l'IT può trarre vantaggio dai vantaggi in termini di costi della multisessione e utilizzare le licenze Windows esistenti per utente anziché le licenze CAL (Client Access License) di Servizi Desktop remoto. Per ulteriori informazioni su licenze e prezzi, vedere Prezzi di [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quanti utenti possono avere contemporaneamente una sessione interattiva su Windows 10 Enterprise multisessione?

Il numero di sessioni interattive che possono essere attive contemporaneamente si basa sulle risorse hardware del sistema (vCPU, memoria, disco e vGPU), su come gli utenti usano le app durante l'accesso a una sessione e da quanto è pesante il carico di lavoro del sistema. Ti consigliamo di convalidare le prestazioni del tuo sistema per capire quanti utenti puoi avere in Windows 10 Enterprise multisessione. Per ulteriori informazioni, vedere Prezzi di [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Perché la mia applicazione segnala Windows 10 Enterprise multisessione come un sistema operativo Server?

Windows 10 Enterprise multisessione è un'edizione virtuale di Windows 10 Enterprise. Una delle differenze è che questo sistema operativo (OS) segnala [il ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) come avere un valore di 3, lo stesso valore di Windows Server. Questa proprietà mantiene il sistema operativo compatibile con gli strumenti di gestione RDSH esistenti, le applicazioni in grado di riconoscere più sessioni RDSH e le ottimizzazioni delle prestazioni del sistema di basso livello per gli ambienti RDSH. Alcuni programmi di installazione delle applicazioni possono bloccare l'installazione in Windows 10 multisessione a seconda se rilevano il ProductType è impostato su Client. Se l'app non viene installata, contatta il fornitore dell'applicazione per una versione aggiornata. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Posso eseguire una sessione multipla di Windows 10 Enterprise in locale?

Windows 10 Enterprise multisessione non può essere eseguito in ambienti di produzione locali perché è ottimizzato per il servizio Desktop virtuale di Windows per Azure. È contro il contratto di licenza per eseguire Windows 10 Enterprise multisessione al di fuori di Azure per scopi di produzione. La multisessione di Windows 10 Enterprise non verrà attivata in servizi di gestione delle chiavi (KMS) locali.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Come si personalizza l'immagine a più sessioni di Windows 10 Enterprise per l'organizzazione?

È possibile avviare una macchina virtuale (VM) in Azure con Windows 10 Windows 10 Enterprise multisessione e personalizzarla installando applicazioni lob, sysprep/generalize e quindi creare un'immagine utilizzando il portale di Azure.You can start a virtual machine (VM) in Azure with Windows 10 Windows 10 Windows 10 Enterprise multi-session and customize it by installing LOB applications, sysprep/generalize, and then create an image using the Azure portal.  
 
Per iniziare, crea una macchina virtuale in Azure con una sessione multipla di Windows 10 Enterprise.To get started, create a VM in Azure with Windows 10 Enterprise multi-session. Anziché avviare la macchina virtuale in Azure, è possibile scaricare direttamente il disco rigido virtuale. Successivamente, potrai usare il disco rigido virtuale scaricato per creare una nuova macchina virtuale di generazione 1 in un PC Windows 10 con Hyper-V abilitato.

Personalizzare l'immagine in base alle proprie esigenze installando applicazioni LOB e sysprep l'immagine. Al termine della personalizzazione, caricare l'immagine in Azure con il disco rigido virtuale all'interno. Successivamente, ottenere Windows Virtual Desktop da Azure Marketplace e usarlo per distribuire un nuovo pool host con l'immagine personalizzata.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Come faccio a gestire Windows 10 Enterprise multisessione dopo la distribuzione?

È possibile usare qualsiasi strumento di configurazione supportato, ma è consigliabile Configuration Manager versione 1906 perché supporta windows 10 Enterprise multisessione. Attualmente stiamo lavorando sul supporto di Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 Enterprise multisessione può essere Azure Active Directory (AD)aggiunto?

La multisessione di Windows 10 Enterprise è attualmente supportata per essere ibrida aggiunta ad Azure AD. Dopo che la multisessione di Windows 10 Enterprise è unita a un dominio, usare l'oggetto Criteri di gruppo esistente per abilitare la registrazione di Azure AD. Per altre informazioni, vedere [Pianificare l'implementazione di join ibrida](../active-directory/devices/hybrid-azuread-join-plan.md)di Azure Active Directory.For more information, see Plan your hybrid Azure Active Directory join implementation .
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Dove posso trovare l'immagine multisessione di Windows 10 Enterprise?

Windows 10 Enterprise multisessione è nella raccolta di Azure.Windows 10 Enterprise multi-session is in the Azure gallery. Per trovarlo, passare al portale di Azure e cercare la versione Windows 10 Enterprise per desktop virtuali. Per un'immagine integrata con Office Pro Plus, passare al portale di Azure e cercare Microsoft Windows 10 - Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Quale immagine multisessione di Windows 10 Enterprise devo usare?

La raccolta di Azure include diverse versioni, tra cui Windows 10 Enterprise multisessione, versione 1809 e Windows 10 Enterprise multisessione, versione 1903. Si consiglia di utilizzare la versione più recente per migliorare le prestazioni e l'affidabilità.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quali versioni multisessione di Windows 10 Enterprise sono supportate?

Windows 10 Enterprise multisessione, versioni 1809 e successive sono supportati e sono disponibili nella raccolta di Azure.Windows 10 Enterprise multi-session, versions 1809 and later are supported and are available in the Azure gallery. Queste versioni seguono lo stesso criterio del ciclo di vita del supporto di Windows 10 Enterprise, il che significa che la versione primaverile è supportata per 18 mesi e la versione autunnale per 30 mesi.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Quale soluzione di gestione dei profili devo usare per Windows 10 Enterprise multisessione?

Ti consigliamo di usare i contenitori di profili FSLogix quando configuri Windows 10 Enterprise in ambienti non persistenti o in altri scenari che richiedono un profilo archiviato centralmente. FSLogix assicura che il profilo utente sia disponibile e aggiornato per ogni sessione utente. È inoltre consigliabile usare il contenitore di profili FSLogix per archiviare un profilo utente in qualsiasi condivisione SMB con le autorizzazioni appropriate, ma è possibile archiviare i profili utente nell'archiviazione BLOB di pagine di Azure, se necessario. Gli utenti di Windows Virtual Desktop possono utilizzare FSLogix senza costi aggiuntivi.
 
Per ulteriori informazioni su come configurare un contenitore di profili FSLogix, vedere [Configurare il contenitore di profili FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Di quale licenza ho bisogno per accedere a Windows 10 Enterprise multisessione?

Per un elenco completo delle licenze applicabili, vedere Prezzi di [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Perché le mie app scompaiono dopo la disconnessione?

Questo accade perché si utilizza Windows 10 Enterprise multisessione con una soluzione di gestione dei profili come FSLogix. La soluzione amministratore o profilo ha configurato il sistema per eliminare i profili utente quando gli utenti si discondiscono. Questa configurazione significa che quando il sistema elimina il profilo utente dopo la disconnessione, rimuove anche tutte le app installate durante la sessione. Se vuoi mantenere le app installate, dovrai chiedere all'amministratore di eseguire il provisioning di queste app per tutti gli utenti nell'ambiente Windows Virtual Desktop.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Come posso assicurarmi che le app non scompaiano quando gli utenti si disconnettono?

La maggior parte degli ambienti virtualizzati è configurata per impostazione predefinita per impedire agli utenti di installare app aggiuntive nei propri profili. Se vuoi assicurarti che un'app non scompaia quando l'utente si disconnette da Windows Virtual Desktop, devi eseguire il provisioning di tale app per tutti i profili utente nell'ambiente. Per altre informazioni sul provisioning delle app, vedere queste risorse:For more information about provisioning apps, check out these resources:

- [Pubblicare app predefinite in Windows Virtual Desktop](publish-apps.md)
- [Opzioni della riga di comando per la manutenzione dei pacchetti dell'app Gestione e manutenzione immagini distribuzione](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Come faccio ad assicurarmi che gli utenti non scarichino e installino app da Microsoft Store?

Puoi disabilitare l'app Microsoft Store per assicurarti che gli utenti non scarichino altre app oltre alle app di cui hai già eseguito il provisioning.

Per disabilitare l'app Store:

1. Creare un nuovo criterio di gruppo.
2. Selezionare**Componenti di Windows Modelli****amministrativi** >  **di Configurazione** > computer .
3. Selezionare **Store**.
4. Selezionare **Applicazione Store**.
5. Selezionare **Disabilitato**, quindi **scegliere OK**.
6. Selezionare **Applica**.
 
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Windows Virtual Desktop e Windows 10 Enterprise multisessione:

- Leggi la [documentazione](overview.md) di Windows Virtual Desktop Preview
- Visita il nostro [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configurare la distribuzione di Windows Virtual Desktop con le [esercitazioni](tenant-setup-azure-active-directory.md) sul desktop virtuale di Windows
