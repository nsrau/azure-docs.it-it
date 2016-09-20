<properties
   pageTitle="Risolvere i problemi di distribuzione di VM Linux - Resource Manager | Microsoft Azure"
   description="Risolvere i problemi della distribuzione Resource Manager quando si crea una nuova macchina virtuale Linux in Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Linux in Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Raccogliere log di controllo

Per avviare la risoluzione dei problemi, raccogliere i log di controllo per identificare l'errore associato al problema. I collegamenti seguenti contengono informazioni dettagliate sul processo da seguire.

[Risolvere i problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operazioni di controllo con Gestione risorse](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**S:** se il sistema operativo Linux è generalizzato e viene caricato e/o acquisito con l'impostazione generalizzata, non si verificheranno errori. Analogamente, se il sistema operativo Linux è specializzato e viene caricato e/o acquisito con l'impostazione specializzata, non si verificheranno errori.

**Errori di caricamento:**

**N<sup>1</sup>:** se il sistema operativo è Linux generalizzato e viene caricato come specializzato, si verificherà un errore di timeout del provisioning perché la VM è bloccata nella fase di provisioning.

**N<sup>2</sup>:** se il sistema operativo è Linux specializzato e viene caricato come generalizzato, si verificherà un errore di provisioning perché la nuova VM viene eseguita con nome computer, nome utente e password originali.

**Risoluzione:**

Per risolvere entrambi questi errori, caricare il disco rigido virtuale originale, disponibile in locale, con la stessa impostazione usata per il sistema operativo (generalizzato/specializzato). Per caricare il disco come generalizzato, ricordarsi di eseguire prima -deprovision.

**Errori di acquisizione:**

**N<sup>3</sup>:** se il sistema operativo è Linux generalizzato e viene acquisito come specializzato, si verificherà un errore di timeout del provisioning perché la VM originale non può essere usata essendo contrassegnata come generalizzata.

**N<sup>4</sup>:** se il sistema operativo è Linux specializzato e viene acquisito come generalizzato, si verificherà un errore di provisioning perché la nuova VM viene eseguita con nome computer, nome utente e password originali. La VM originale, inoltre, non può essere usata perché è contrassegnata come specializzata.

**Risoluzione:**

Per risolvere entrambi questi errori, eliminare l'immagine corrente dal portale e [acquisirla di nuovo dai dischi rigidi virtuali correnti](virtual-machines-linux-capture-image.md) con la stessa impostazione usata per il sistema operativo (generalizzato/specializzato).

## Problema: Immagine personalizzata/della raccolta/del marketplace - errore di allocazione
Questo errore si verifica nelle situazioni in cui la nuova richiesta di VM viene aggiunta a un cluster che non può supportare le dimensioni della VM richieste oppure non ha spazio disponibile sufficiente per soddisfare la richiesta.

**Causa 1:** il cluster non supporta le dimensioni della VM richieste.

**Risoluzione 1:**

- Ripetere la richiesta usando una VM di dimensioni inferiori.
- Se le dimensioni della VM richieste non possono essere modificate:
  - Arrestare tutte le VM nel set di disponibilità. Fare clic su **Gruppi di risorse** > *gruppo di risorse personale* > **Risorse** > *set di disponibilità personale* > **Macchine virtuali** > *macchina virtuale personale* > **Arresta**.
  - Dopo l'arresto di tutte le VM, creare la nuova VM con le dimensioni desiderate.
  - Avviare prima di tutto la nuova VM e quindi selezionare le VM arrestate e fare clic su **Avvia**.

**Causa 2:** il cluster non ha risorse disponibili.

**Risoluzione 2:**

- Ripetere la richiesta in un secondo momento.
- Se la nuova VM può far parte di un set di disponibilità diverso
  - Creare una nuova VM in un altro set di disponibilità nella stessa area.
  - Aggiungere la nuova VM alla stessa rete virtuale.

## Passaggi successivi
Se si incontrano problemi quando si avvia una VM Linux arrestata o si ridimensiona una VM Linux esistente in Azure, vedere [Risolvere i problemi della distribuzione di Resource Manager con il riavvio e il ridimensionamento di una macchina virtuale Linux esistente in Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).

<!---HONumber=AcomDC_0907_2016-->