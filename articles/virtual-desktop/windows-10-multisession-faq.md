---
title: Domande frequenti su più sessioni di Windows 10 Enterprise-Azure
description: Domande frequenti e procedure consigliate per l'uso di Windows 10 Enterprise multisessione per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: helohr
ms.openlocfilehash: 10724407b8ba5568b38a844f2bf475060e2b7699
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227674"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Domande frequenti su Windows 10 Enterprise multisessione

Questo articolo risponde alle domande frequenti e illustra le procedure consigliate per la multisessione Enterprise di Windows 10.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Che cos'è Windows 10 Enterprise multisessione? 

Windows 10 Enterprise Multi-Session, in precedenza noto come Windows 10 Enterprise for Virtual Desktops (EVD), è un nuovo host sessione Desktop remoto che consente più sessioni interattive simultanee, che in precedenza potevano essere eseguite solo da Windows Server. Questa funzionalità offre agli utenti un'esperienza familiare di Windows 10, anche se può trarre vantaggio dai vantaggi economici della multisessione e utilizzare licenze Windows per utente esistenti anziché licenze CAL (Client Access License) di RDS. Per ulteriori informazioni sulle licenze e i prezzi, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Quanti utenti possono simultaneamente una sessione interattiva in più sessioni di Windows 10 Enterprise?

Il numero di sessioni interattive che possono essere attive contemporaneamente si basa sulle risorse hardware del sistema (vCPU, Memory, disk e vGPU), su come gli utenti usano le app durante l'accesso a una sessione e sul carico di lavoro del sistema. Si consiglia di convalidare le prestazioni del sistema per comprendere il numero di utenti che è possibile avere per la multisessione Enterprise di Windows 10. Per altre informazioni, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Perché l'applicazione segnala Windows 10 Enterprise multisessione come sistema operativo server?

Windows 10 Enterprise Multisession è un'edizione virtuale di Windows 10 Enterprise. Una delle differenze consiste nel fatto che questo sistema operativo segnala il [ProductType](https://docs.microsoft.com/windows/desktop/cimwin32prov/win32-operatingsystem) come avente un valore pari a 3, ovvero lo stesso valore di Windows Server. Questa proprietà mantiene il sistema operativo compatibile con gli strumenti di gestione RDSH esistenti, le applicazioni RDSH con più sessioni e le ottimizzazioni delle prestazioni del sistema di basso livello per gli ambienti RDSH. Alcuni programmi di installazione dell'applicazione possono bloccare l'installazione in più sessioni di Windows 10, a seconda che rilevino che ProductType sia impostato su client. Se l'app non viene installata, contattare il fornitore dell'applicazione per una versione aggiornata. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>È possibile eseguire la multisessione Enterprise di Windows 10 in locale?

La multisessione Enterprise di Windows 10 non può essere eseguita in ambienti di produzione locali perché è ottimizzata per il servizio desktop virtuale di Windows per Azure. Si tratta di un contratto di licenza per l'esecuzione di più sessioni di Windows 10 Enterprise all'esterno di Azure per scopi di produzione. La multisessione Enterprise di Windows 10 non viene attivata per i servizi di gestione delle chiavi (KMS) locali.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Ricerca per categorie personalizzare l'immagine di Windows 10 Enterprise multisessione per la mia organizzazione?

È possibile avviare una macchina virtuale (VM) in Azure con Windows 10 Enterprise multisessione e personalizzarla installando applicazioni LOB, Sysprep/generalizzate, quindi creare un'immagine usando il portale di Azure.  
 
Per iniziare, creare una macchina virtuale in Azure con Windows 10 Enterprise multisessione. Anziché avviare la VM in Azure, è possibile scaricare direttamente il disco rigido virtuale. Successivamente, sarà possibile usare il disco rigido virtuale scaricato per creare una nuova macchina virtuale di prima generazione in un PC Windows 10 con Hyper-V abilitato.

Personalizzare l'immagine in base alle esigenze mediante l'installazione di applicazioni LOB e l'immagine di Sysprep. Al termine della personalizzazione, caricare l'immagine in Azure con il VHD all'interno di. Successivamente, ottenere il desktop virtuale di Windows da Azure Marketplace e usarlo per distribuire un nuovo pool di host con l'immagine personalizzata.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Ricerca per categorie gestire la multisessione Enterprise di Windows 10 dopo la distribuzione?

È possibile usare qualsiasi strumento di configurazione supportato, ma è consigliabile System Center Configuration Manager 1906 perché supporta la multisessione Enterprise di Windows 10. Stiamo lavorando al supporto Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>È possibile unire in join Azure Active Directory (AD) Windows 10 Enterprise in più sessioni?

La multisessione Enterprise di Windows 10 è attualmente supportata come ibrido Azure AD-join. Dopo che la multisessione di Windows 10 Enterprise è aggiunta a un dominio, usare l'oggetto Criteri di gruppo esistente per abilitare la registrazione Azure AD. Per altre informazioni, vedere [pianificare l'implementazione del join di Azure Active Directory ibrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Dove è possibile trovare l'immagine multisessione di Windows 10 Enterprise?

La funzionalità multisessione di Windows 10 Enterprise è presente nella raccolta di Azure. Per trovarlo, passare alla portale di Azure e cercare la versione Windows 10 Enterprise per desktop virtuali. Per un'immagine integrata con Office Pro Plus, passare alla portale di Azure e cercare Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Quale immagine di Windows 10 Enterprise per più sessioni è consigliabile usare?

La raccolta di Azure include diverse versioni, tra cui Windows 10 Enterprise Multisession, versione 1809 e Windows 10 Enterprise Multisession, versione 1903. Per migliorare le prestazioni e l'affidabilità, è consigliabile usare la versione più recente.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Quali versioni di Windows 10 Enterprise multisessione sono supportate?

Windows 10 Enterprise multisessione, versioni 1809 e successive sono supportate e sono disponibili nella raccolta di Azure. Queste versioni seguono gli stessi criteri del ciclo di vita del supporto di Windows 10 Enterprise, il che significa che la versione Spring è supportata per 18 mesi e la versione autunnale per 30 mesi.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Quale soluzione di gestione dei profili è consigliabile usare per la multisessione Enterprise di Windows 10?

Si consiglia di usare i contenitori del profilo FSLogix quando si configura Windows 10 Enterprise in ambienti non permanenti o in altri scenari che richiedono un profilo archiviato centralmente. FSLogix garantisce che il profilo utente sia disponibile e aggiornato per ogni sessione utente. Si consiglia anche di usare il contenitore del profilo FSLogix per archiviare un profilo utente in qualsiasi condivisione SMB con le autorizzazioni appropriate, ma è possibile archiviare i profili utente nell'archiviazione BLOB di pagine di Azure, se necessario. Gli utenti di desktop virtuali Windows possono utilizzare FSLogix senza costi aggiuntivi.
 
Per altre informazioni su come configurare un contenitore del profilo FSLogix, vedere [configurare il contenitore del profilo FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Quale licenza è necessario per accedere a multisessione Enterprise di Windows 10?

Per un elenco completo delle licenze applicabili, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
 
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su desktop virtuale Windows e multisessione Enterprise di Windows 10:

- Leggi la [documentazione di anteprima del desktop virtuale Windows](overview.md)
- Visita il [desktop virtuale Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Configurare la distribuzione di desktop virtuali Windows con le [esercitazioni sul desktop virtuale di Windows](tenant-setup-azure-active-directory.md)
