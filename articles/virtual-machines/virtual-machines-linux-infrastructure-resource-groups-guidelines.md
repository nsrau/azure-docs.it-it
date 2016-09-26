<properties
	pageTitle="Linee guida sui gruppi di risorse | Microsoft Azure"
	description="Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione dei gruppi di risorse nei servizi di infrastruttura di Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>  

# Linee guida sui gruppi di risorse di Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Questo articolo si incentra su come creare in modo logico un ambiente e raggruppare tutti i componenti in gruppi di risorse.


## Linee guida sui gruppi di risorse

Decisioni:

- Si desidera creare gruppi di risorse con i componenti dell'infrastruttura di base oppure tramite la distribuzione di applicazioni complete?
- È necessario limitare l'accesso ai gruppi di risorse usando controlli di accesso in base ai ruoli?

Attività:

- Definire i componenti dell'infrastruttura di base e i gruppi di risorse dedicati necessari.
- Esaminare la modalità di implementazione dei modelli di Resource Manager per ottenere distribuzioni coerenti e riproducibili.
- Definire i ruoli di accesso utente necessari per controllare l'accesso ai gruppi di risorse.
- Creare il set di gruppi di risorse usando la usando la convenzione di denominazione scelta. È possibile usare l'interfaccia della riga di comando o il portale di Azure.


## Gruppi di risorse

Azure consente di raggruppare in modo logico risorse correlate, ad esempio account di archiviazione, reti virtuali e macchine virtuali per distribuirle, gestirle ed eseguirne la manutenzione come una singola entità. Questo approccio semplifica la distribuzione delle applicazioni e al contempo mantiene insieme tutte le risorse correlate per facilitarne la gestione o per consentire altri accessi al gruppo di risorse. Per informazioni complete sui gruppi di risorse, leggere la [Panoramica di Azure Resource Manager](../resource-group-overview.md).

Una funzionalità chiave dei gruppi di risorse è la possibilità di creare l'ambiente tramite un file JSON che dichiara le risorse di archiviazione, rete e calcolo. È anche possibile definire qualsiasi script personalizzato o configurazioni correlati da applicare. Tramite i modelli JSON è possibile creare distribuzioni coerenti e riproducibili delle applicazioni. Ciò consente la compilazione di un ambiente nella fase di sviluppo e l'uso dello stesso modello per creare una distribuzione di produzione, o viceversa. Per altre informazioni sull'uso dei modelli, leggere [la procedura dettagliata sui modelli](../resource-manager-template-walkthrough.md) che descrive i passaggi per la compilazione di un modello JSON.

Esistono due diversi approcci alla progettazione dell'ambiente con gruppi di risorse:

- Gruppi di risorse per ogni distribuzione dell'applicazione, che contengono account di archiviazione, reti virtuali e subnet, macchine virtuali, servizi di bilanciamento del carico e così via.
- Gruppi di risorse centralizzate che contengono la rete virtuale di base e le subnet oppure gli account di archiviazione. Le applicazioni si trovano quindi nei propri gruppi di risorse che contengono solo macchine virtuali, servizi di bilanciamento del carico, interfacce di rete e così via.

Procedendo con la scalabilità orizzontale, i gruppi di risorse centralizzati contenenti la rete virtuale e le subnet semplificano la creazione di connessioni di rete cross-premise per opzioni di connettività ibrida. L'approccio alternativo prevede di avere singole applicazioni dotate di una propria rete virtuale che richiede configurazione e manutenzione. [I controlli di accesso in base al ruolo](../active-directory/role-based-access-control-what-is.md) rappresentano una modalità granulare per controllare l'accesso ai gruppi di risorse. Per le applicazioni di produzione, è possibile controllare gli utenti che possono accedere alle risorse. Per le risorse dell'infrastruttura di base è possibile limitare l'accesso solo ai tecnici dell'infrastruttura che le usano. I proprietari dell'applicazione possono accedere solo ai componenti dell'applicazione nel proprio gruppo di risorse e non all'infrastruttura di Azure di base dell'ambiente. Quando si progetta l'ambiente, tenere in considerazione gli utenti che dovranno accedere alle risorse e creare gruppi di risorse adeguati.


## Passaggi successivi

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->