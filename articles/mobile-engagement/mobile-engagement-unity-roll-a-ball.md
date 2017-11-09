---
title: esercitazione su Roll a Ball di Unity
description: Procedura per creare il classico gioco Roll a Ball di Unity, un prerequisito per tutte le esercitazioni di Unity per Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a id="unity-roll-a-ball"></a>Creare il gioco Roll a Ball di Unity
Questa esercitazione illustra i passaggi principali di un' [esercitazione su Roll a Ball di Unity](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)leggermente modificata. Questo gioco di esempio è costituito da un oggetto "player" sferico controllato dall'utente dell'app e lo scopo del gioco è di raccogliere oggetti da collezione facendo scontrare l'oggetto player con questi oggetti da collezione. Ciò presuppone una certa conoscenza di base dell'ambiente dell'editor di Unity. In caso di problemi, vedere l'esercitazione completa. 

### <a name="setting-up-the-game"></a>Configurazione del gioco
I passaggi seguenti sono tratti dall' [esercitazione di Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Aprire l'**editor Unity** e fare clic su **New**. 
   
    ![][51] 
2. Fornire **nome del progetto** & **percorso**, selezionare **3D** e fare clic su **Create project**.
   
    ![][52]
3. Salvare la scena predefinita appena creata come parte del nuovo progetto con il nome **MiniGame** all'interno di una nuova cartella **\_Scenes** nella cartella **Assets**:
   
    ![][53]
4. Creare il campo di gioco selezionando **3D Object -> Plane** e assegnare all'oggetto il nome **Ground**
   
    ![][1]
5. Reimpostare il componente di trasformazione per questo oggetto **Ground** in modo che si trovi in corrispondenza dell'origine. 
   
    ![][3]
6. Deselezionare **Show Grid** dal **menu Gizmos** per l'oggetto **Ground**.
   
    ![][4]
7. Aggiornare il componente **Scale** per l'oggetto **Ground** con le dimensioni [X = 2, Y = 1, Z = 2]. 
   
    ![][5]
8. Aggiungere un nuovo elemento al progetto andando su **3D Object -> Sphere** e assegnare all'oggetto sferico il nome **Player**. 
   
    ![][6]
9. Selezionare l'oggetto **Player** e fare clic su **Reset Transform** in modo analogo all'oggetto Plane. 
10. Aggiornare il componente **Transform -> Position -> Y Coordinate** per Player Y assegnando il valore 0,5.  
    
    ![][7]
11. Creare nel progetto una nuova cartella denominata **Materials** dove verrà creato il materiale per colorare il giocatore. 
12. Creare un nuovo **materiale** chiamato **Background** in questa cartella. 
    
    ![][8]
13. Aggiornare il colore del materiale aggiornando la proprietà **Albedo** . È possibile selezionare i valori RGB [0,32,64]. 
    
    ![][9]
14. Trascinare questo materiale nella visualizzazione scena per applicare il colore all'oggetto **Ground** . 
    
    ![][10]
15. Aggiornare infine il **Transform -> Rotation -> Y** assegnando il valore 60 nell'oggetto Directional Light per maggiore chiarezza. 
    
    ![][12]

### <a name="moving-the-player"></a>Spostamento del player
I passaggi seguenti sono tratti dall' [esercitazione di Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Aggiungere un componente **RigidBody** all'oggetto **Player**. 
   
    ![][13]
2. Creare una nuova cartella denominata **Scripts** nel progetto. 
3. Fare clic su **Add Component-> New Script -> C# Script**. Assegnargli il nome **PlayerController** e fare clic su **Create and Add**. Verrà creato uno script che verrà associato all'oggetto Player.  
   
    ![][14]
4. Spostare lo script nella cartella **Scripts** nel progetto. 
5. Aprire lo script per modificarlo nell'editor di script preferito, aggiornare il codice dello script con il codice seguente e salvarlo. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Si noti che lo script precedente usa una proprietà **Speed** . Nell'editor di Unity impostare la proprietà Speed su 10.  
   
    ![][15]
7. Selezionare **Play** nell'editor di Unity. Ora sarà possibile controllare con la tastiera la pallina che dovrebbe ruotare e muoversi. 

### <a name="moving-the-camera"></a>Spostamento della videocamera
I passaggi di seguito sono presi dall'[esercitazione su Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) e limiteranno **Main Camera** all'oggetto **Player**. 

1. Aggiornare **Transform.Position** con i valori X = 0, Y = 10,5, Z = -10.  
2. Impostare **Transform.Rotation** su X = 45, Y = 0, Z= 0.  
   
    ![][16]
3. Aggiungere un nuovo script denominato **CameraController** a **MainCamera** e spostarlo nella cartella Scripts. 
   
    ![][17]
4. Aprire lo script per la modifica e aggiungervi il codice seguente:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. Nell'ambiente di Unity trascinare la variabile Player nello slot Player per l'oggetto Main Camera per poter creare un'associazione tra di essi. 
   
    ![][18]
6. Se ora si preme Play nell'editor di Unity e si ruota l'oggetto Player Ball, si potrà osservare che la videocamera lo segue nel movimento.  

### <a name="setting-up-the-play-area"></a>Configurazione dell'area di gioco
I passaggi seguenti sono tratti dall' [esercitazione di Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Verranno creati i muri che circondano il campo in modo che l'oggetto Player Ball non esca dall'area di gioco mentre è in movimento. 

1. Fare clic su **Create -> Create Empty -> Game Object** e assegnare il nome **Walls**
   
    ![][19]
2. In questo oggetto Walls creare un nuovo oggetto andando su **3D Object -> Cube** e assegnare il nome "West wall". 
   
    ![][20]
3. Aggiornare **Transform -> Position** e **Transform -> Scale** per l'oggetto West Wall. 
   
    ![][21]
4. Duplicare West wall per creare un elemento **East wall** con la posizione e la scala di trasformazione aggiornate. 
   
    ![][22]
5. Duplicare East wall in modo da creare un oggetto **North wall** con la posizione di trasformazione e le proporzioni aggiornate. 
   
    ![][23]
6. Duplicare North wall in modo da creare un oggetto **South wall** con la posizione di trasformazione e le proporzioni aggiornate. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Creazione di oggetti da collezione
I passaggi seguenti sono tratti dall' [esercitazione di Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Verranno creati alcuni oggetti dall'aspetto interessante che costituiranno il set di oggetti da collezione che l'oggetto Player Ball deve raccogliere scontrandosi con essi. 

1. Creare un nuovo **oggetto 3D Cube** e denominarlo Pickup. 
2. Regolare **Transform -> Rotation** & **Transform -> Scale** dell'oggetto Pickup. 
   
    ![][25]
3. Creare e collegare un **nuovo script C#** chiamato **Rotator** all'oggetto Pickup. Verificare di inserire lo script nella cartella Scripts. 
   
    ![][26]
4. Aprire lo script per la modifica e aggiornarlo come segue: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Selezionare ora la modalità Play nell'editor di Unity per far ruotare l'oggetto Pickup sul proprio asse.
6. Creare una nuova cartella denominata **Prefabs** 
   
    ![][27]
7. Trascinare l'oggetto **Pickup** e inserirlo nella cartella Prefabs.
   
    ![][28]
8. Creare un nuovo **oggetto Empty Game** chiamato **Pickups**. Reimpostarne la posizione sull'origine e quindi trascinare l'oggetto Pickup sotto l'oggetto gioco.  
   
    ![][29]
9. Duplicare l'oggetto **Pickup** e distribuirlo nell'oggetto **Ground** attorno all'oggetto **Player** aggiornando in modo appropriato i valori **X e Z di Transform.Position**. 
   
    ![][30]
10. Creare un **nuovo materiale** chiamato **Pickup** e impostarlo sul colore rosso aggiornando la **proprietà Albedo** tramite una procedura simile a quella seguita per l'aggiornamento dell'oggetto Ground. 
    
    ![][31]
11. Applicare il materiale a tutti e 4 gli oggetti Pickup.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Raccolta degli oggetti Pickup
I passaggi seguenti sono tratti dall' [esercitazione di Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Verrà aggiornato il Player in modo che possa raccogliere gli oggetti Pickup scontrandosi con essi. 

1. Aprire lo script **PlayerController** collegato all'oggetto Player per la modifica e aggiornarlo come segue:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Creare un nuovo **tag** chiamato **Pick Up** (deve corrispondere ai contenuti dello script)  
   
    ![][33]
   
    ![][34]
3. Applicare questo **Tag** all'oggetto Pickup di Prefab. 
   
    ![][35]
4. Abilitare la casella di controllo **IsTrigger** per l'oggetto Prefab.
   
    ![][36]
5. Aggiungere un Rigid body all'oggetto Pickup di Prefab. Per ottimizzare le prestazioni, il collider statico usato verrà sostituito con un collider dinamico. 
   
    ![][37]
6. Selezionare infine la proprietà **IsKinematic** dell'oggetto Prefab. 
   
    ![][38]
7. Premere **Play** nell'editor Unity e sarà possibile giocare a **Roll a Ball** spostando l'oggetto Player tramite i tasti della tastiera per indicare la direzione. 

### <a name="updating-the-game-for-mobile-play"></a>Aggiornamento del gioco per la riproduzione su dispositivo mobile
Con le sezioni precedenti si è conclusa l'esercitazione di base di Unity. Ora il gioco verrà modificato in modo che sia semplice da usare sui dispositivi mobili. Si noti che finora per il test è stato usato l'input da tastiera. Verrà ora modificato in modo da poter controllare il giocatore tramite il movimento del telefono, ad esempio usando l'accelerometro come input. 

Aprire lo script **PlayerController** per modificare e aggiornare il metodo **FixedUpdate** in modo da usare il movimento dell'accelerometro per spostare l'oggetto Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Con questa esercitazione termina la creazione di un gioco di base con Unity, che può essere distribuito e usato sul dispositivo preferito. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













