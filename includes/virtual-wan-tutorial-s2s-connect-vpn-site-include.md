---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450850"
---
1. Selezionare **Connetti siti VPN** per visualizzare la pagina **Connetti siti**.

    ![connect](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "connessione")

   Completare i campi seguenti:

   * Immettere una chiave precondivisa. Se non si immette una chiave, Azure ne genera automaticamente una.
   * Selezionare le impostazioni relative a protocollo e IPsec. Fare riferimento a [dettagli di IPSec predefiniti/personalizzati] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Selezionare l'opzione appropriata per **Propaga route predefinita**. L'opzione **Abilita** consente all'hub virtuale di propagare a questa connessione una route predefinita appresa. Questo flag consente la propagazione della route predefinita a una connessione solo se tale route è già stata appresa dall'hub della rete WAN virtuale a seguito della distribuzione di un firewall nell'hub o se per un altro sito connesso è abilitato il tunneling forzato. La route predefinita non ha origine nell'hub della rete WAN virtuale.

2. Selezionare **Connetti**.
3. Nel giro di qualche minuto il sito visualizzerà lo stato di connessione e di connettività.

   ![Stato](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Stato della connessione:** si tratta dello stato della risorsa di Azure per la connessione che connette il sito VPN al gateway VPN dell'hub di Azure. Dopo che questa operazione del piano di controllo ha esito positivo, il gateway VPN di Azure e il dispositivo VPN locale tenteranno di stabilire la connettività.

   **Stato connettività:** si tratta dello stato (percorso dati) di connettività effettivo tra il gateway VPN di Azure nell'hub e il sito VPN. Può indicare uno degli stati seguenti:

    * **Sconosciuto**: questo stato in genere viene visualizzato se i sistemi back-end stanno tentando di passare a un altro stato.
    * **Connessione**: il gateway VPN di Azure sta tentando di raggiungere l'effettivo sito VPN locale.
    * **Connesso**: è stata stabilita la connettività tra il gateway VPN di Azure e il sito VPN locale.
    * **Disconnesso**: questo stato viene visualizzato se, per un motivo qualsiasi (in locale o in Azure), la connessione è stata interrotta.
4. All'interno di un sito VPN hub è inoltre possibile eseguire queste operazioni: 

   * Modificare o eliminare la connessione VPN.
   * Eliminare il sito nel portale di Azure.
   * Scaricare una configurazione specifica del ramo per informazioni dettagliate sul lato Azure usando il menu di scelta rapida (...) accanto al sito. Se si vuole scaricare la configurazione per tutti i siti connessi nell'hub, scegliere **Scarica configurazione VPN** dal menu superiore.
