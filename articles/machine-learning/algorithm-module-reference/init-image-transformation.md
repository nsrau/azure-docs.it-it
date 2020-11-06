---
title: Trasformazione immagine init image Transformationply
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Init Image Transformation in Azure Machine Learning Designer per inizializzare la trasformazione immagine.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421278"
---
# <a name="init-image-transformation"></a>Init Image Transformation

Questo articolo descrive come usare il modulo **init Image Transformation** in Azure Machine Learning Designer per inizializzare la trasformazione dell'immagine in modo da specificare come si desidera trasformare l'immagine.

## <a name="how-to-configure-init-image-transformation"></a>Come configurare la trasformazione dell'immagine init

1.  Aggiungere il modulo **init Image Transformation** alla pipeline nella finestra di progettazione. 

2.  Per **Resize** specificare se ridimensionare l'immagine del PIL di input alla dimensione specificata. Se si sceglie ' true ', per impostazione predefinita è possibile specificare le dimensioni dell' **immagine di output desiderata, per** impostazione predefinita 256. 

3.  Per **Crop Center** specificare se ritagliare l'immagine del PIL specificata al centro. Se si sceglie ' true ', per impostazione predefinita è possibile specificare la dimensione dell'immagine di output desiderata per il ritaglio in **dimensioni di ritaglio** , per impostazione predefinita 224.  

4.  Per **riempimento** specificare se riempire l'immagine del PIL specificata su tutti i lati con il valore di riempimento 0. Se si sceglie ' true ', è possibile specificare la spaziatura interna (il numero di pixel da aggiungere) a ogni bordo della **spaziatura interna**.

5.  Per la **jitter dei colori** , specificare se modificare in modo casuale la luminosità, il contrasto e la saturazione di un'immagine.

6.  Per le **gradazioni di grigio** specificare se convertire l'immagine in scala di grigi.

7.  Per il **ritaglio casuale** ridimensionato, specificare se ritagliare l'immagine del PIL specificata in dimensioni casuali e proporzioni. Un ritaglio di dimensioni casuali (compreso tra 0,08 e 1,0) delle dimensioni originali e una percentuale casuale (compresa tra 3/4 e 4/3) delle proporzioni originali. Il ritaglio viene infine ridimensionato in base alle dimensioni specificate.
    Questa operazione viene in genere usata per il training delle reti di inizio. Se si sceglie ' true ', è possibile specificare la dimensione di output prevista di ogni bordo in **dimensioni casuali** , per impostazione predefinita 256.

8.  Per il **ritaglio casuale** , specificare se ritagliare l'immagine del PIL specificata in una posizione casuale. Se si sceglie ' true ', per impostazione predefinita è possibile specificare le dimensioni di output desiderate per il ritaglio in **dimensioni di ritaglio casuale** , per impostazione predefinita 224.

9.  Per la **vibrazione orizzontale casuale** , specificare se capovolgere orizzontalmente l'immagine del PIL specificata con la probabilità 0,5.

10.  Per il **capovolgimento verticale casuale** specificare se capovolgere verticalmente l'immagine del PIL specificata in modo casuale con la probabilità 0,5.

11.  Per la **rotazione casuale** , specificare se ruotare l'immagine in base all'angolo. Se si sceglie ' true ', è possibile specificare in un intervallo di gradi impostando i **gradi di rotazione casuale** , ovvero (-Degrees, + Degrees), per impostazione predefinita 0.

12.  Per **affinità casuale** , specificare se la trasformazione affine casuale dell'immagine mantiene il centro invariante. Se si sceglie ' true ', è possibile specificare un intervallo di gradi per la selezione tra i **gradi di affinità casuale** , che indica (-gradi, + gradi), per impostazione predefinita 0.

13.  Per la **scala di grigi casuale** , specificare se convertire in modo casuale l'immagine in scala di grigi con probabilità 0,1.

14.  Per la **prospettiva casuale** , specificare se eseguire in modo casuale la trasformazione prospettiva dell'immagine PIL specificata con la probabilità 0,5.


16.  Connetti al modulo [Applica trasformazione immagine](apply-image-transformation.md) per applicare la trasformazione specificata in precedenza al set di dati dell'immagine di input.

17. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine della trasformazione, è possibile trovare le immagini trasformate nell'output del modulo [Apply Image Transformation](apply-image-transformation.md) .


## <a name="technical-notes"></a>Note tecniche  

[https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html)Per ulteriori informazioni sulla trasformazione immagine, vedere.

###  <a name="module-parameters"></a>Parametri del modulo  

| Nome                    | Range   | Type    | Predefinito | Descrizione                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Ridimensionamento                  | Qualsiasi     | Boolean | True    | Ridimensionare l'immagine del PIL di input alla dimensione specificata |
| Dimensione                    | >= 1     | Integer | 256     | Specificare le dimensioni di output desiderate          |
| Centra ritaglio             | Qualsiasi     | Boolean | True    | Ritaglia l'immagine del PIL specificata al centro  |
| Dimensioni ritaglio               | >= 1     | Integer | 224     | Specificare le dimensioni di output desiderate per il ritaglio |
| Pad                     | Qualsiasi     | Boolean | Falso   | Riempimento dell'immagine PIL specificata su tutti i lati con il valore "pad" specificato |
| Riempimento                 | >= 0     | Integer | 0       | Spaziatura interna per ogni bordo                   |
| Jitter colore            | Qualsiasi     | Boolean | Falso   | Modificare in modo casuale la luminosità, il contrasto e la saturazione di un'immagine |
| Gradazioni di grigio               | Qualsiasi     | Boolean | Falso   | Converti immagine in scala di grigi               |
| Ritaglio casuale ridimensionato     | Qualsiasi     | Boolean | Falso   | Ritagliare l'immagine del PIL specificata in dimensioni casuali e proporzioni |
| Dimensioni casuali             | >= 1     | Integer | 256     | Dimensioni di output previste per ogni Edge        |
| Ritaglio casuale             | Qualsiasi     | Boolean | Falso   | Ritagliare l'immagine del PIL specificata in una posizione casuale |
| Dimensioni taglia casuale        | >= 1     | Integer | 224     | Dimensioni di output desiderate per il ritaglio          |
| Capovolgimento orizzontale casuale  | Qualsiasi     | Boolean | True    | Capovolge orizzontalmente l'immagine del PIL specificata in modo casuale con una determinata probabilità |
| Capovolgimento verticale casuale    | Qualsiasi     | Boolean | Falso   | Capovolge verticalmente l'immagine del PIL specificata in modo casuale con una determinata probabilità |
| Rotazione casuale         | Qualsiasi     | Boolean | Falso   | Ruotare l'immagine in base all'angolo                |
| Gradi di rotazione casuale | [0180] | Integer | 0       | Intervallo di gradi da selezionare          |
| Affinità casuale           | Qualsiasi     | Boolean | Falso   | Trasformazione affine casuale dell'immagine che mantiene invariante il centro |
| Gradi affini casuali   | [0180] | Integer | 0       | Intervallo di gradi da selezionare          |
| Scala di grigi casuale        | Qualsiasi     | Boolean | Falso   | Convertire in modo casuale l'immagine in scala di grigi con probabilità 0,1 |
| Prospettiva casuale      | Qualsiasi     | Boolean | Falso   | Esegue in modo casuale la trasformazione prospettiva dell'immagine PIL specificata con la probabilità 0,5 |
| Cancellazione casuale          | Qualsiasi     | Boolean | Falso   | Seleziona in modo casuale un'area rettangolare in un'immagine e ne cancella i pixel con probabilità 0,5 |

###  <a name="output"></a>Output  

| Nome                        | Type                    | Description                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Trasformazione immagine di output | TransformationDirectory | Trasformazione dell'immagine di output che può essere connessa al modulo **Applica trasformazione immagine** . |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 