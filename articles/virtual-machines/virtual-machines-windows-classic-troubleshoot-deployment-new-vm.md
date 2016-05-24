<properties
   pageTitle="Risolvere i problemi di distribuzione di VM Windows - Classica | Microsoft Azure"
   description="Risolvere i problemi della distribuzione classica quando si crea una nuova macchina virtuale Windows in Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/06/2016"
  ms.author="cjiang"/>

# Risolvere i problemi della distribuzione classica con la creazione di una nuova macchina virtuale Windows in Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Raccogliere log di controllo

Per avviare la risoluzione dei problemi, raccogliere i log di controllo per identificare l'errore associato al problema.

Nel portale di Azure fare clic su **Esplora** > **Macchine virtuali** > *macchina virtuale Windows personale* > **Impostazioni** > **Log di controllo**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**S:** se il sistema operativo Windows è generalizzato e viene caricato e/o acquisito con l'impostazione generalizzata, non si verificheranno errori. Analogamente, se il sistema operativo Windows è specializzato e viene caricato e/o acquisito con l'impostazione specializzata, non si verificheranno errori.

**Errori di caricamento:**

**N<sup>1</sup>:** se il sistema operativo è Windows generalizzato e viene caricato come specializzato, si verificherà un errore di timeout del provisioning con la VM bloccata nella schermata di Configurazione guidata.

**N<sup>2</sup>:** se il sistema operativo è Windows specializzato e viene caricato come generalizzato, si verificherà un errore di provisioning con la VM bloccata nella schermata di Configurazione guidata perché la nuova VM è in esecuzione con nome computer, nome utente e password originali.

**Risoluzione:**

Per risolvere entrambi questi errori, caricare il disco rigido virtuale originale, disponibile in locale, con la stessa impostazione usata per il sistema operativo (generalizzato/specializzato). Per caricare il disco come generalizzato, ricordarsi di eseguire prima sysprep. Per altre informazioni, vedere [Creare e caricare un disco rigido virtuale con Windows Server in Azure](virtual-machines-windows-classic-createupload-vhd.md).

**Errori di acquisizione:**

**N<sup>3</sup>:** se il sistema operativo è Windows generalizzato e viene acquisito come specializzato, si verificherà un errore di timeout del provisioning perché la VM originale non può essere usata essendo contrassegnata come generalizzata.

**N<sup>4</sup>:** se il sistema operativo è Windows specializzato e viene acquisito come generalizzato, si verificherà un errore di provisioning perché la nuova VM è in esecuzione con nome computer, nome utente e password originali. La VM originale, inoltre, non può essere usata perché è contrassegnata come specializzata.

**Risoluzione:**

Per risolvere entrambi questi errori, eliminare l'immagine corrente dal portale e [acquisirla di nuovo dai dischi rigidi virtuali correnti](virtual-machines-windows-classic-capture-image.md) con la stessa impostazione usata per il sistema operativo (generalizzato/specializzato).

## Problema: Immagine personalizzata/della raccolta/del marketplace - errore di allocazione
Questo errore si verifica nelle situazioni in cui la nuova richiesta di VM viene inviata a un cluster che non ha spazio disponibile sufficiente per soddisfare la richiesta o che non può supportare le dimensioni della VM richieste. Non è possibile combinare serie di VM diverse nello stesso servizio cloud. Se si vuole quindi creare una nuova VM con dimensioni diverse rispetto a quelle che il servizio cloud può supportare, la richiesta di calcolo non riuscirà.

In base ai vincoli del servizio cloud usato per creare la nuova VM, si potrebbe verificare un errore causato da una delle due situazioni seguenti.

**Causa 1:** il servizio cloud è aggiunto a un cluster specifico oppure è collegato a un gruppo di affinità e perciò aggiunto a un cluster specifico in base alla progettazione. Le nuove richieste di risorse di calcolo in quel gruppo di affinità vengono quindi provate nello stesso cluster in cui sono ospitate le risorse esistenti. Tuttavia, lo stesso cluster potrebbe non supportare le dimensioni della VM richieste o non ha spazio disponibile sufficiente, causando un errore di allocazione. Questo vale indipendentemente dal fatto che le nuove risorse vengano create tramite un servizio cloud nuovo o esistente.

**Risoluzione 1:**

- Creare un nuovo servizio cloud e associarlo a un'area o una rete virtuale basata sull'area.
- Crea una nuova VM nel nuovo servizio cloud. Se si verifica un errore durante il tentativo di creare un nuovo servizio cloud, riprovare in un secondo momento o cambiare l'area per il servizio cloud.

> [AZURE.IMPORTANT] Se si sta provando a creare una nuova VM in un servizio cloud esistente, ma è stato necessario creare un nuovo servizio cloud per la nuova VM, è possibile scegliere di consolidare tutte le VM nello stesso servizio cloud. A questo scopo, eliminare le VM nel servizio cloud esistente e acquisirle di nuovo dai relativi dischi nel nuovo servizio cloud. È tuttavia importante ricordare che il nuovo servizio cloud avrà un nuovo nome e un nuovo indirizzo VIP, quindi sarà necessario aggiornarli per tutte le dipendenze che attualmente usano queste informazioni per il servizio cloud esistente.

**Causa 2:** il servizio cloud è associato a una rete virtuale collegata a un gruppo di affinità e perciò è aggiunto a un cluster specifico in base alla progettazione. Tutte le nuove richieste di risorse di calcolo in quel gruppo di affinità vengono quindi provate nello stesso cluster in cui sono ospitate le risorse esistenti. Tuttavia, lo stesso cluster potrebbe non supportare le dimensioni della VM richieste o non ha spazio disponibile sufficiente, causando un errore di allocazione. Questo vale indipendentemente dal fatto che le nuove risorse vengano create tramite un servizio cloud nuovo o esistente.

**Risoluzione 2:**

- Crea una nuova rete virtuale a livello di area
- Creare la nuova VM nella nuova rete virtuale.
- [Connettere la rete virtuale esistente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) alla nuova rete virtuale. Altre informazioni sulle [reti virtuali a livello di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). In alternativa, è possibile [eseguire la migrazione della rete virtuale basata su gruppi di affinità alla rete virtuale a livello di area](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/) e quindi creare la nuova VM.

<!---HONumber=AcomDC_0511_2016-->