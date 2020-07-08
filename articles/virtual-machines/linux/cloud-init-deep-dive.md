---
title: Informazioni su cloud-init
description: Approfondimento per informazioni sul provisioning di una macchina virtuale di Azure con cloud-init.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042076"
---
# <a name="diving-deeper-into-cloud-init"></a>Approfondimenti su cloud-init
Per altre informazioni su [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) o su come risolverlo a un livello più profondo, è necessario comprenderne il funzionamento. Questo documento evidenzia le parti importanti e spiega le specifiche di Azure.

Quando cloud-init è incluso in un'immagine generalizzata e una macchina virtuale viene creata da tale immagine, elabora le configurazioni e viene eseguita fino a 5 fasi durante l'avvio iniziale. Queste fasi sono importanti, come illustrato in che punto cloud-init applicherà le configurazioni. 


## <a name="understand-cloud-init-configuration"></a>Informazioni sulla configurazione di cloud-init
La configurazione di una macchina virtuale per l'esecuzione su una piattaforma, significa che cloud-init deve applicare più configurazioni, come consumer di immagini, le configurazioni principali che interagiscono con è `User data` (CustomData), che supporta più formati, questi sono documentati [qui](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). È anche possibile aggiungere ed eseguire script (/var/lib/cloud/scripts) per una configurazione aggiuntiva. di seguito viene illustrato in modo più dettagliato questo aspetto.

Alcune configurazioni sono già state preparate in immagini di Azure Marketplace disponibili con cloud-init, ad esempio:

1. **Cloud data source** -cloud-init contiene codice che può interagire con le piattaforme cloud, denominate "origini dati". Quando viene creata una VM da un'immagine cloud-init in [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure), cloud-init carica l'origine dati di Azure, che interagisce con gli endpoint dei metadati di Azure per ottenere la configurazione specifica della macchina virtuale.
2. **Configurazione Runtime** (/Run/cloud-init)
3. **Image config** (/etc/cloud), ad `/etc/cloud/cloud.cfg` esempio `/etc/cloud/cloud.cfg.d/*.cfg` . Un esempio di come viene usato in Azure, è comune per le immagini del sistema operativo Linux con cloud-init avere una direttiva dell'origine dati di Azure, che indica a cloud-init quale origine dati deve usare, questo consente di risparmiare tempo di cloud-init:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Fasi di avvio cloud-init (configurazione elaborazione)

Quando si effettua il provisioning con cloud-init, sono disponibili 5 fasi di avvio, che elaborano la configurazione e vengono visualizzate nei log.

1. [Fase del generatore](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): il generatore di sistema cloud-init viene avviato e determina che cloud-init deve essere incluso negli obiettivi di avvio e, in tal caso, Abilita cloud-init. 

2. [Fase locale di cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): qui cloud-init cerca l'origine dati "Azure" locale che consente a cloud-init di interfacciarsi con Azure e applicare una configurazione di rete, incluso il fallback.

3. [Fase di inizializzazione cloud-init (rete)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): la rete deve essere online e le informazioni sulla tabella di route e NIC devono essere generate. In questa fase verranno eseguiti i moduli elencati in `cloud_init_modules` in/etc/Cloud/Cloud.cfg. La macchina virtuale in Azure verrà montata, il disco temporaneo verrà formattato, verrà impostato il nome host, insieme ad altre attività.

   Di seguito sono riportate alcune delle operazioni seguenti `cloud_init_modules` :
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Dopo questa fase, cloud-init segnalerà alla piattaforma Azure che è stato effettuato il provisioning della macchina virtuale. Alcuni moduli potrebbero avere avuto esito negativo, non tutti gli errori dei moduli comporteranno un errore di provisioning.

4. [Fase di configurazione di cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): in questa fase verranno eseguiti i moduli in `cloud_config_modules` definiti ed elencati in/etc/Cloud/Cloud.cfg.


5. [Fase finale cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): in questa fase finale i moduli in `cloud_final_modules` , elencati in/etc/Cloud/Cloud.cfg, verranno eseguiti. Ecco i moduli che devono essere eseguiti in ritardo durante l'esecuzione del processo di avvio, ad esempio installazioni di pacchetti ed esecuzione di script e così via. 

   -   Durante questa fase, è possibile eseguire gli script inserendoli nelle directory sotto `/var/lib/cloud/scripts` :
   - `per-boot`-script all'interno di questa directory, eseguiti a ogni riavvio
   - `per-instance`-gli script all'interno di questa directory vengono eseguiti quando una nuova istanza viene avviata per la prima volta
   - `per-once`-gli script all'interno di questa directory vengono eseguiti una sola volta

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei problemi di cloud-init](cloud-init-troubleshooting.md).
