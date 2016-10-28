Questa condizione valuterà il campo dell'indirizzo di posta elettronica di ogni nuovo lead Salesforce. Se l'indirizzo di posta elettronica contiene *amazon.com*, il risultato della condizione sarà *True*.

1. Selezionare **+ nuovo passaggio**.  
![Immagine di condizione Salesforce 1](./media/connectors-create-api-salesforce/condition-1.png)  
- Selezionare **Aggiungi una condizione**.  
![Immagine di condizione Salesforce 2](./media/connectors-create-api-salesforce/condition-2.png)  
- Selezionare **Scegli un valore**.  
![Immagine di condizione Salesforce 3](./media/connectors-create-api-salesforce/condition-3.png)  
- Selezionare il token *E-mail* dal lead del trigger.  
![Immagine di condizione Salesforce 4](./media/connectors-create-api-salesforce/condition-4.png)  
- Selezionare *Contiene*.  
![Immagine di condizione Salesforce 5](./media/connectors-create-api-salesforce/condition-5.png)  
- Selezionare **Scegli un valore** nella parte inferiore del controllo.  
![Immagine di condizione Salesforce 6](./media/connectors-create-api-salesforce/condition-6.png)  
- Immettere *amazon.com* come valore su cui valutare l'indirizzo di posta elettronica del nuovo lead. Se l'indirizzo di posta elettronica contiene *amazon.com*, la condizione lo valuta come *True* ed è possibile procedere con gli altri passaggi nell'app per la logica.  
![Immagine di condizione Salesforce 7](./media/connectors-create-api-salesforce/condition-7.png)  
- Salvare le app per la logica.

<!---HONumber=AcomDC_0727_2016-->
