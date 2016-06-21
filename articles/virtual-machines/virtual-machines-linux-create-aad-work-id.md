<properties
   pageTitle="Creare un'identità di lavoro o scuola in AAD | Microsoft Azure"
   description="Informazioni su come creare un'identità di lavoro o scuola in Azure Active Directory da usare con le macchine virtuali Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="06/06/2016"
   ms.author="rasquill"/>

# Creazione di un'identità di lavoro o scuola in Azure Active Directory da usare con macchine virtuali Linux

Se si è creato un account Azure personale, oppure si ha un sottoscrizione MSDN personale e si è creato l'account Azure per sfruttare i vantaggi dei crediti MSDN in Azure, si è usata un'identità *account Microsoft*. Numerose funzionalità di Azure (ad esempio i [modelli dei gruppi di risorse](../resource-group-overview.md)) richiedono un account di lavoro o scuola (un'identità gestita da Azure Active Directory) per funzionare. Per creare un nuovo account aziendale o dell’istituto d’istruzione è possibile seguire le seguenti istruzioni poichè fortunatamente, uno degli aspetti migliori dell'account Azure personale sta nel fatto che è dotato di un dominio predefinito di Azure Active Directory, che è possibile usare per creare un nuovo account aziendale o dell’istituto d’istruzione da usare con le funzionalità di Azure che lo richiedono.

Tuttavia, le modifiche più recenti consentono di gestire la sottoscrizione con qualsiasi tipo di account Azure tramite il metodo di accesso interattivo `azure login` descritto [qui](../xplat-cli-connect.md). È possibile utilizzare tale meccanismo oppure seguire le istruzioni che seguono. È anche possibile [creare un'identità di lavoro o scuola in Azure Active Directory da usare con macchine virtuali Windows](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]

<!---HONumber=AcomDC_0608_2016-->