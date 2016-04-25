<properties
   pageTitle="Panoramica su come creare e distribuire un'offerta in Marketplace | Microsoft Azure"
   description="Informazioni sui passaggi necessari a diventare uno sviluppatore Microsoft approvato e creare e distribuire un'immagine di macchina virtuale, un modello, un servizio dati o un servizio per sviluppatori in Azure Marketplace"
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
   ms.date="04/07/2016"
   ms.author="hascipio" />

# Come pubblicare un'offerta in Azure Marketplace
Questo articolo viene fornito per aiutare uno sviluppatore a creare e distribuire una soluzione in Azure Marketplace che altri clienti e partner di Azure possono acquistare e usare.

La prima operazione da eseguire come editore è definire il tipo di soluzione offerta dalla propria azienda. L’Azure Marketplace supporta varie soluzioni e ognuna di esse richiede un set di lavoro leggermente diverso per la corretta pubblicazione in Marketplace.

Tipi di soluzioni:

- Immagine di macchina virtuale
- Servizio per gli sviluppatori
- Servizio dati
- Modello di soluzione

Alcuni passaggi sono condivisi tra i diversi tipi di soluzioni. Questo articolo fornisce una breve panoramica dei passaggi da completare per qualsiasi tipo di soluzione.

> [AZURE.NOTE] Prima di iniziare le operazioni in Azure Marketplace, è necessario avere ottenuto la pre-approvazione. Questo non è applicabile per gli editori di servizi dati.

||Immagine di macchina virtuale |Servizio per gli sviluppatori | Servizio dati | Modello di soluzione |
|----|----|----|----|----|
| **Ottenere la pre-approvazione** | [Microsoft Azure Certified][link-certification] | Anteprima privata | N/D | [Microsoft Azure Certified][link-certification] |
| **Passaggio 1: Registrare l'account per sviluppatore** | [Account per sviluppatore Microsoft: creazione e registrazione][link-accts] | Anteprima privata | [Account per sviluppatore Microsoft: creazione e registrazione][link-accts] | [Account per sviluppatore Microsoft: creazione e registrazione][link-accts] |
|**Passaggio 2: Creare l'offerta**| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| Anteprima privata | [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)|
|| [Prerequisiti tecnici per le VM][link-single-vm-prereq] | Anteprima privata | [Prerequisiti tecnici per il servizio dati](marketplace-publishing-data-service-creation-prerequisites.md) | [Prerequisiti tecnici per il modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md) |
||[Guida alla pubblicazione di immagini VM][link-single-vm] | Anteprima privata | [Guida alla pubblicazione del servizio dati](marketplace-publishing-data-service-creation.md) | [Guida alla pubblicazione di un modello di soluzione](marketplace-publishing-solution-template-creation.md) |
|| [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] | Anteprima privata | [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] | [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] |
| **Passaggio 3: Push dell'offerta nello staging** | [Testare l'offerta VM nello staging](marketplace-publishing-vm-image-test-in-staging.md) | Anteprima privata | [Test dell'offerta del servizio dati nello staging](marketplace-publishing-data-service-test-in-staging.md) | [Test del modello di soluzione nello staging](marketplace-publishing-solution-template-test-in-staging.md) |
| **Passaggio 4: Distribuire l'offerta nel marketplace** | [Distribuire l'offerta nel marketplace][link-pushprod] | Anteprima privata | [Distribuire l'offerta nel marketplace][link-pushprod] | [Distribuire l'offerta nel marketplace][link-pushprod] |

## Supporto
- [Ottenere supporto come editore][suppt-general]
- [Informazioni sui report di Insights per i venditori][suppt-rpt-insights]
- [Informazioni sui report sui proventi][suppt-rpt-payouts]
- [Come modificare l'incentivo per i rivenditori per i Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
- [Risoluzione dei problemi di pubblicazione comuni in Marketplace][suppt-common]

## Risorse aggiuntive
- Per altre informazioni sui portali usati, vedere [Portali necessari](marketplace-publishing-portals.md)

**Macchine virtuali**

- [Configurazione di Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines-windows-hero-tutorial/)

**Servizi dati**

- [Mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping.md)
- [Nodi di mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [Esempi di mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

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

<!---HONumber=AcomDC_0413_2016-->