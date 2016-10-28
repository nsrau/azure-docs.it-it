<properties
   pageTitle="Creare un nome di dominio completo (FQDN) per una VM nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un nome di dominio completo o FQDN per un Gestore risorse basato su macchina virtuale nel portale di Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# Creare un nome di dominio completo nel portale di Azure.
Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com) usando il modello di distribuzione Azure Resource Manager, il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se, per impostazione predefinita, il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN (Fully Qualified Domain Name), è possibile aggiungerne uno dopo aver creato la VM. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

È ora possibile connettersi in modalità remota alla macchina virtuale usando questo nome DNS, ad esempio `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## Passaggi successivi
Quando la macchina virtuale ha un indirizzo IP pubblico e un nome DNS, è possibile distribuire framework applicazioni o servizi comuni, ad esempio nginx, MongoDB, Docker e così via.

Per suggerimenti sulla creazione di distribuzioni di Azure, vedere la pagina relativa all'[uso di Azure Resource Manager](../resource-group-overview.md).

<!---HONumber=AcomDC_0824_2016-->