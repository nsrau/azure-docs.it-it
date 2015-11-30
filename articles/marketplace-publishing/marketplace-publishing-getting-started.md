<properties
   pageTitle="Panoramica su come creare e distribuire un'offerta in Marketplace | Microsoft Azure"
   description="Informazioni sui passaggi necessari per diventare un venditore Microsoft approvato e per creare e distribuire un'immagine di macchina virtuale, un modello, un servizio dati o un servizio per sviluppatori in Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Come pubblicare un'offerta in Azure Marketplace
Questo articolo viene fornito per aiutare un venditore a creare e distribuire una soluzione in Azure Marketplace per altri clienti di Azure e i partner ad acquistare e utilizzare.

La prima operazione da eseguire come editore è definire il tipo di soluzione offerta dalla propria azienda. L’Azure Marketplace supporta varie soluzioni e ognuna di esse richiede un set di lavoro leggermente diverso per la corretta pubblicazione in Marketplace.

Tipi di soluzioni:

- Immagine di macchina virtuale
- Servizio per gli sviluppatori
- Servizio dati
- Modello di soluzione

Alcuni passaggi sono condivisi tra i diversi tipi di soluzioni. Questo articolo fornisce una breve panoramica dei passaggi da completare per qualsiasi tipo di soluzione.

> [AZURE.NOTE]Prima di iniziare le operazioni in Azure Marketplace, è necessario avere ottenuto la pre-approvazione. Questo non è applicabile per gli editori di servizi dati.

|| Immagine di macchina virtuale | Servizio per sviluppatori | Servizio dati | Modello di soluzione | |---|---|---|---|---| | **Ottenere l'approvazione** | [Certificato Microsoft Azure][link-certification] | [Certificato Microsoft Azure][link-certification] | n/d | [Certificato Microsoft Azure][link-certification] | | **Passaggio 1: registrare l'account venditore** | [Account venditore Microsoft: creazione e registrazione][link-accts] | [Account venditore Microsoft: creazione e registrazione][link-accts] | [Account venditore Microsoft: creazione e registrazione][link-accts] | [Account venditore Microsoft: creazione e registrazione][link-accts] | |**Passaggio 2: Creare la proposta**| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| | | [Prerequisiti tecnici della VM][link-single-vm-prereq] | Prerequisiti tecnici del servizio dello sviluppatore | Prerequisiti tecnici del servizio dati | [Prerequisiti tecnici del modello della soluzione ](marketplace-publishing-solution-template-creation-prerequisites.md) | | |[Guida alla pubblicazione dell’immagine di macchina virtuale][link-single-vm] | Guida alla pubblicazione del servizio per sviluppatori | Guida alla pubblicazione del servizio dati | [Guida alla pubblicazione del modello di soluzione](marketplace-publishing-solution-template-creation.md) | | | [Guida al contenuto marketing di Azure Marketplace][link-pushstaging] | [Guida al contenuto marketing di Azure Marketplace][link-pushstaging] | [Guida al contenuto marketing di Azure Marketplace][link-pushstaging] | [Guida al contenuto marketing di Azure Marketplace][link-pushstaging] | | **Passaggio 3: effettuare il Push dell'offerta di gestione temporanea** | [Testare l'offerta della macchina virtuale in gestione temporanea](marketplace-publishing-vm-image-test-in-staging.md) | Testare l'offerta del servizio per sviluppatori in gestione temporanea | Testare l’offerta del servizio dati in gestione temporanea | [Testare il modello di soluzione in gestione temporanea](marketplace-publishing-solution-template-test-in-staging.md) | | **Passaggio 4: distribuire la proposta in Marketplace** | [Distribuire la proposta in Marketplace][link-pushprod] | [Distribuire la proposta in Marketplace][link-pushprod] | [Distribuire la proposta in Marketplace][link-pushprod] | [Distribuire la proposta in Marketplace][link-pushprod] |

## Supporto
- [Ottenere supporto come editore][suppt-general]
- [Informazioni sui report di Insights per i venditori][suppt-rpt-insights]
- [Informazioni sui report sui proventi][suppt-rpt-payouts]
- [Risoluzione dei problemi di pubblicazione comuni in Marketplace][suppt-common]

## Risorse aggiuntive
- Per altre informazioni sui portali usati, vedere [Portali necessari](marketplace-publishing-portals.md)

**Macchine virtuali**

- [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines-windows-tutorial/)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=Nov15_HO4-->