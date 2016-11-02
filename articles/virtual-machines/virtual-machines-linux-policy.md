<properties
	pageTitle="Applicare criteri a macchine virtuali di Azure Resource Manager | Microsoft Azure"
	description="Come applicare criteri a una macchina virtuale Linux di Azure Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="singhkay"/>

# Applicare criteri a macchine virtuali di Azure Resource Manager

Tramite i criteri è possibile imporre diverse convenzioni e regole in tutta l'organizzazione. L'imposizione del comportamento desiderato consente di attenuare i rischi, contribuendo nello stesso tempo al successo dell'organizzazione. Questo articolo descrive come usare criteri di Azure Resource Manager per definire il comportamento desiderato per le macchine virtuali dell'organizzazione.

Per eseguire questa operazione, seguire questa procedura:

1. Informazioni di base sui criteri di Azure Resource Manager
2. Definire criteri per la macchina virtuale
3. Creare i criteri
4. Applicare i criteri

## Informazioni di base sui criteri di Azure Resource Manager

Come introduzione ai criteri di Azure Resource Manager è consigliabile leggere l'articolo riportato di seguito prima di continuare la procedura descritta in questo articolo. L'articolo riportato di seguito descrive la definizione di base e la struttura dei criteri e la modalità di valutazione dei criteri stessi. L'articolo fornisce anche alcuni esempi di definizioni di criteri.

* [Usare i criteri per gestire le risorse e controllare l'accesso](../resource-manager-policy.md)

## Definire criteri per la macchina virtuale

Uno scenario comune per un'azienda potrebbe essere la possibilità per gli utenti di creare macchine virtuali solo da sistemi operativi specifici di cui sia stata testata la compatibilità con un'applicazione line-of-business. Usando criteri di Azure Resource Manager è possibile eseguire questa attività in pochi passaggi. Questo criterio di esempio consente di creare solo macchine virtuali Ubuntu 14.04.2-LTS. La definizione del criterio ha l'aspetto seguente

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

Il criterio precedente può essere facilmente modificato per uno scenario in cui per una distribuzione di macchine virtuali si vuole consentire l'uso di qualsiasi immagine Ubuntu LTS. È sufficiente la seguente modifica

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### Campi delle proprietà di una macchina virtuale

La tabella seguente descrive le proprietà delle macchine virtuali che possono essere usate come campi all'interno di una definizione di criteri. Per altre informazioni sui campi dei criteri, vedere l'articolo seguente:

* [Campi e origini](../resource-manager-policy.md#fields-and-sources)


| Nome campo | Descrizione |
|----------------|----------------------------------------------------|
| imagePublisher | Specifica l'editore dell'immagine |
| imageOffer | Specifica l'offerta per l'editore dell'immagine prescelto |
| imageSku | Specifica lo SKU per l'offerta prescelta |
| imageVersion | Specifica la versione di immagine per lo SKU prescelto |

## Creare i criteri

Un criterio può essere creato facilmente usando l'API REST direttamente o i cmdlet di PowerShell. Per creare i criteri, vedere l'articolo seguente:

* [Creazione dei criteri](../resource-manager-policy.md#creating-a-policy)


## Applicare i criteri

Dopo aver creato i criteri è necessario applicarli a un ambito definito. L'ambito può essere una sottoscrizione, un gruppo di risorse o persino una risorsa. Per applicare i criteri, vedere l'articolo seguente:

* [Creazione dei criteri](../resource-manager-policy.md#applying-a-policy)

<!---HONumber=AcomDC_0824_2016-->