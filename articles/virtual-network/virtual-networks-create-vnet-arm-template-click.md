<properties
   pageTitle="Creare una rete virtuale usando un modello di Gestione risorse di Azure | Microsoft Azure"
   description="Informazioni su come creare una rete virtuale usando un modello di Gestione risorse di Azure | Gestione risorse."
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos"/>

# Creare una rete virtuale usando un modello di Gestione risorse di Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo documento viene illustrata la creazione di una rete virtuale usando il modello di distribuzione di gestione delle risorse. È inoltre possibile [creare una rete virtuale nel modello di distribuzione classica](virtual-networks-create-vnet-classic-pportal.md).

Verrà illustrato come scaricare e modificare un modello di Gestione risorse di Azure esistente da GitHub e distribuire il modello da GitHub, PowerShell e dall'interfaccia della riga di comando di Azure.

Se si sta distribuendo semplicemente il modello di Gestione risorse di Azure direttamente da GitHub, senza alcuna modifica, ignorare il passaggio per [distribuire un modello da github](#deploy-the-arm-template-by-using-click-to-deploy).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!---HONumber=AcomDC_0211_2016-->