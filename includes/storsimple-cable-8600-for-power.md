<!--author=alkohli last changed: 9/16/15-->


#### Cablare il dispositivo per l'alimentazione
> [!NOTE]
> Entrambi gli enclosure nel dispositivo StorSimple includono PCM ridondanti. Per ogni enclosure entrambi i PCM devono essere installati e collegati a una fonte di alimentazione diversa per assicurare la disponibilità elevata.
> 
> 

1. Assicurarsi che gli interruttori di alimentazione di tutti i moduli PCM siano in posizione OFF.
2. Nell'enclosure principale, collegare i cavi di alimentazione a entrambi i moduli PCM. I cavi di alimentazione sono identificati in rosso nella seguente figura del cablaggio di alimentazione.
3. Verificare che i due moduli PCM dell'enclosure principale usino fonti di alimentazione separate.
4. Collegare i cavi di alimentazione alle unità PDU (Power Distribution Unit) rack come illustrato nella seguente figura del cablaggio di alimentazione.
5. Ripetere i passaggi da 2 a 4 per l'enclosure EBOD.
6. Accendere l'enclosure EBOD girando l'interruttore di alimentazione di ciascun modulo PCM su ON.
7. Verificare che l'enclosure EBOD sia attiva assicurandosi che i LED dei controller EBOD (di colore verde sul retro dell'enclosure) siano accesi.
8. Accendere l'enclosure principale girando l'interruttore di ciascun modulo PCM su ON.
9. Verificare che il sistema sia attivo assicurandosi che i LED dei controller del dispositivo siano accesi.
10. Verificare che il collegamento tra il controller EBOD e il controller del dispositivo sia attivo, assicurandosi che i quattro LED accanto alla porta SAS sul controller EBOD siano verdi.
    
    > [!IMPORTANT]
    > Per garantire la disponibilità elevata del sistema, si consiglia vivamente di attenersi allo schema di cablaggio dell'alimentazione illustrato nella seguente figura.
    > 
    > 
    
    ![Cablare il dispositivo 4U per l'alimentazione](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Cablaggio di alimentazione**
    
    | Etichetta | Descrizione |
    |:--- |:--- |
    | 1 |Enclosure principale |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Controller 0 |
    | 5 |Controller 1 |
    | 6 |Controller 0 EBOD |
    | 7 |Controller 1 EBOD |
    | 8 |Chassis EBOD |
    | 9 |PDU |

<!---HONumber=Oct15_HO3-->