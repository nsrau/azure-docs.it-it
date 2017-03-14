---
title: Configurare una rete virtuale di Azure (classica) - File di configurazione di rete | Documentazione Microsoft
description: Informazioni su come modificare le reti virtuali (classiche) esportando, modificando e importando un file di configurazione di rete tramite il portale di Azure (classico).
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 3827dc0958c51fa0c4ecb1a2e8e3b7bbed42a75a
ms.lasthandoff: 02/28/2017


---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurare una rete virtuale (classica) usando un file di configurazione di rete
È possibile configurare una rete virtuale (classica) mediante il portale di Azure (classico) o usando un file di configurazione di rete. Non è possibile creare o modificare una rete virtuale mediante il modello di distribuzione di Azure Resource Manager usando un file di configurazione di rete. Non è possibile neppure usare il portale di Azure per creare o modificare una rete virtuale (classica).

## <a name="creating-and-modifying-a-network-configuration-file"></a>Creazione e modifica di un file di configurazione di rete
Il modo più semplice per creare un file di configurazione di rete consiste nell'esportare le impostazioni di rete da una rete virtuale esistente (classica), quindi modificare il file in modo da includervi le impostazioni che si vuole configurare per le reti virtuali.

Per modificare il file di configurazione di rete, è sufficiente aprire il file, apportare le modifiche appropriate e salvarlo. È possibile usare qualsiasi editor *xml* per apportare modifiche al file di configurazione di rete. 

È consigliabile attenersi alle istruzioni per le [impostazioni dello schema del file di configurazione](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera una subnet con un elemento distribuito come **in uso**. Quando una subnet è in uso, non può essere modificata. Prima di apportare modifiche, spostare qualsiasi elemento distribuito alla subnet in una subnet diversa che non è sottoposta a modifiche.   Vedere [Spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-azure-portal-classic"></a>Esportare e importare le impostazioni di rete virtuale mediante il portale di Azure (classico)
È possibile importare ed esportare le impostazioni di configurazione di rete contenute nel file di configurazione di rete usando PowerShell o il portale di gestione. Le istruzioni riportate di seguito consentono di eseguire l'esportazione e l'importazione tramite il portale di gestione. 

### <a name="to-export-your-network-settings"></a>Per esportare le impostazioni di rete
Quando si esporta, tutte le impostazioni per le reti virtuali nella sottoscrizione verranno scritte in un file XML. 

1. Accedere al [portale di Azure (classico)](https://manage.windowsazure.com/).
2. Nel portale, in fondo alla pagina **Reti**, fare clic su **Esporta**. 
3. Nella finestra **Esporta configurazione di rete** , verificare di aver selezionato la sottoscrizione per la quale si vogliono esportare le impostazioni di rete. Quindi, fare clic sul segno di spunta in basso a destra. 
4. Quando richiesto, salvare il file *NetworkConfig.xml* nel percorso desiderato.

### <a name="to-import-your-network-settings"></a>Per importare le impostazioni di rete
1. Nel portale, nel riquadro di spostamento in basso a sinistra, fare clic su **Nuovo**.
2. Fare clic su **Servizi di rete** -> **Rete virtuale** -> **Importa configurazione**.
3. Nella pagina **Importa file di configurazione della rete** passare al file di configurazione della rete e quindi fare clic su **Avanti**.
4. Nella pagina **Compilazione della rete** verranno visualizzate le informazioni sulla schermata che mostra quali sezioni della configurazione di rete verranno modificate o create. Se le modifiche sembrano corrette, fare clic sul segno di spunta per continuare con l'aggiornamento o la creazione della rete virtuale. 


