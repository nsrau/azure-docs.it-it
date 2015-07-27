
<properties 
    pageTitle="Informazioni sul ridimensionamento di una rete virtuale in RemoteApp"
    description="Informazioni sui requisiti relativi agli indirizzi IP per RemoteApp in esecuzione con una rete virtuale" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# Informazioni sul ridimensionamento di una rete virtuale in RemoteApp

Quando si usa RemoteApp con una rete virtuale (VNET), RemoteApp usa gli indirizzi IP all'interno della subnet. In base alla scala del servizio RemoteApp, è necessario assicurarsi che la subnet abbia indirizzi IP sufficienti per le macchine virtuali RemoteApp. Queste indicazioni di ridimensionamento non sono complete se si osserva in che modo RemoteApp esegue dinamicamente lo spin-up e lo spin-down delle macchine virtuali all'interno di una raccolta, tuttavia consentono di stimare l'intervallo di subnet. Ciò è particolarmente importante perché, quando si include un servizio RemoteApp in una rete virtuale, non è possibile aumentare le dimensioni della subnet senza rimuovere RemoteApp.

Per ogni raccolta RemoteApp da eseguire alla capacità massima devono essere disponibili 100 indirizzi IP. Ad esempio, se si ha una raccolta RemoteApp nel piano Standard e si vuole avere un numero massimo di 500 utenti, sono necessari 100 indirizzi IP per la raccolta. Analogamente, in un piano Basic con 800 utenti sono necessari 100 indirizzi IP per una raccolta RemoteApp. Se si prevede un numero minore di utenti (inferiore al numero massimo), è possibile ridurre gli indirizzi IP per ogni raccolta. Il requisito minimo per le dimensioni della subnet è di 30 indirizzi IP (/27).
 

<!---HONumber=July15_HO2-->