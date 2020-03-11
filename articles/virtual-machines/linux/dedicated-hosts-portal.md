---
title: Distribuire host dedicati di Azure usando il portale di Azure
description: Distribuire le macchine virtuali in host dedicati usando il portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 5af09cf7ef6c811a239a64c5c6349c3625316177
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970746"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Distribuire macchine virtuali in host dedicati tramite il portale

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
1. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace, cercare e selezionare **Ubuntu Server 16.04 LTS** di Canonical e quindi scegliere **Crea**.
1. Nella scheda **nozioni di base** , in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta, quindi selezionare *myDedicatedHostsRG* come **gruppo di risorse**. 
1. Sotto **Dettagli dell'istanza**, digitare *myVM* come **nome della macchina virtuale** e scegliere *Stati Uniti orientali* come **Posizione**.
1. In **Opzioni di disponibilità** selezionare **zona di disponibilità**Selezionare *1* nell'elenco a discesa.
1. Per dimensioni selezionare **modifica dimensioni**. Nell'elenco delle dimensioni disponibili scegliere una dalla serie Esv3, ad esempio **E2S standard V3**. Potrebbe essere necessario cancellare il filtro per visualizzare tutte le dimensioni disponibili.
1. Sotto **Account amministratore**, selezionare **Chiave pubblica SSH**, digitare il nome utente, quindi incollare la chiave pubblica nella casella di testo. Rimuovere gli eventuali spazi iniziali o finali nella chiave pubblica.

    ![Account amministratore](./media/quick-create-portal/administrator-account.png)

1. In **regole porta in ingresso** > **porte in ingresso pubbliche**, scegliere **Consenti porte selezionate** , quindi selezionare **SSH (22)** nell'elenco a discesa. 
1. Nella parte superiore della pagina **selezionare la scheda** **Avanzate** e nella sezione **host** Selezionare *myHostGroup* per **gruppo host** *e host host.* 
    ![selezionare il gruppo host e l'host](./media/dedicated-hosts-portal/advanced.png)
1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi + Crea** nella parte inferiore della pagina.
1. Quando viene visualizzato il messaggio che la convalida è stata superata, selezionare **Crea**.

La distribuzione della macchina virtuale richiederà alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere Cenni preliminari sugli [host dedicati](dedicated-hosts.md) .

- È disponibile un modello di esempio [, che](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)USA sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile distribuire un host dedicato usando l' [interfaccia](dedicated-hosts-cli.md)della riga di comando di Azure.



