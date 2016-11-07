<?php
class Client{
  private $api_key = "";
  private $accountname = '';
  private $file = 'log.txt';

  function _contruct(){}
  
  function Get($clientid){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/clients/".$clientid.".xml?api_key=".$this->api_key;
    
    $logResource = fopen($this->file, "w+");

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
  
    $result = curl_exec($ch);
    
    if(curl_errno($ch)){
			var_dump(curl_error($ch));
		}
		
    curl_close($ch);
    
    fclose($logResource); //fecha ficheiro de log
    
    return $result;
  }
}

class Quote{
  private $api_key = "";
  private $accountname = '';
  private $file = 'log.txt';

  function _contruct(){}
  
  function Create($date, $due_date, $clientName, $clientCode, $itemName, $itemDescription, $itemPrice, $itemQuantity){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/quotes.xml?api_key=".$this->api_key;
  
    $logResource = fopen($this->file, "w+");
    
    $invoice = new SimpleXMLElement("<quote></quote>");
    $invoice->addChild('date', $date);
    $invoice->addChild('due_date', $due_date);

    $invoiceCliente = $invoice->addChild('client');
    $invoiceCliente->addChild('name', $clientName);
    $invoiceCliente->addChild('code', $clientCode);

    $invoiceItems = $invoice->addChild('items');
    $invoiceItems->addAttribute('type', 'array');
    
    $invoiceItem = $invoiceItems->addChild('item');
    $invoiceItem->addChild('name', $itemName);
    $invoiceItem->addChild('description', $itemDescription);
    $invoiceItem->addChild('unit_price', $itemPrice);
    $invoiceItem->addChild('quantity', $itemQuantity);
    
    $data = $invoice->asXML();

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
    
    $result = curl_exec($ch);
    
		if(curl_errno($ch)){
			return curl_error($ch);
		}
		
		curl_close($ch);
		
		fclose($logResource); //closes log file
		
		//get client info
		echo $result;
    $createXml = new SimpleXMLElement($result); //parse xml
		$clientid = $createXml->client->id;
		$quoteid = $createXml->id;
		
    //get client info
    $client = new Client();
    $clientGetReturn = $client->Get($clientid);
    $clientGetXml = new SimpleXMLElement($clientGetReturn); //parse xml
    
    //changes quote state to final
    $state = "finalized";
    $comment = "";
    $this->ChangeState($quoteid,$state,$comment);
    
    //send email
    $email = $clientGetXml->preferred_contact->email; //get mail
    $body = "Boa tarde,<p>Em anexo segue o orçamento do seu pedido.</p><p><a href='http://www.bodybymimo.com/dev/bbm/acceptquote.php?quoteid=".$quoteid."'>Clique aqui</a> para gerar a sua fatura</p>";
    $subject = "Orçamento ".$quoteid;
    $this->Email($quoteid, "fabioborges91@gmail.com", "", "", $body, $subject);
    
    return $result;
  }
  
  function Get($quoteid){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/quotes/".$quoteid.".xml?api_key=".$this->api_key;
    
    $logResource = fopen($this->file, "w+");

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
  
    $result = curl_exec($ch);
    
    if(curl_errno($ch)){
			return curl_error($ch);
		}
		
    curl_close($ch);
    
    fclose($logResource); //fecha ficheiro de log
    
    return $result;
  }
  
  function Email($quoteid, $email, $cc, $bcc, $body, $subject){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/quotes/".$quoteid."/email-document.xml?api_key=".$this->api_key;
    $logResource = fopen($this->file, "w+");
    
    $input = new SimpleXMLElement("<message></message>");
    
    $inputCliente = $input->addChild("client");
    $inputCliente->addChild("email", $email);
    $inputCliente->addChild("save", 0);
    
    $input->addChild("subject", $subject);
    $input->addChild("body", $body);
    $input->addChild("cc", $cc);
    $input->addChild("bcc", $bcc);

    $data = $input->asXML();

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
    
    $result = curl_exec($ch);
    
    if(curl_errno($ch)){
			return curl_error($ch);
		}
		
    curl_close($ch);
    
    fclose($logResource); //fecha ficheiro de log
    
    return $result;
  }
  
  function ChangeState($quoteid, $state, $comment){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/quotes/".$quoteid."/change-state.xml?api_key=".$this->api_key;
  
    $logResource = fopen($this->file, "w+");
    
    $invoice = new SimpleXMLElement("<quote></quote>");
    $invoice->addChild('state', $state);
    $invoice->addChild('comment', $comment);

    $data = $invoice->asXML();
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
    
    $result = curl_exec($ch);
    
		if(curl_errno($ch)){
			return curl_error($ch);
		}
		
		curl_close($ch);
		
		fclose($logResource); //fecha ficheiro de log
  }
}

class Invoice{
  private $api_key = "";
  private $accountname = '';
  private $file = 'log.txt';

  function _contruct(){}
  
  function Create($date, $due_date, $clientName, $clientCode, $itemName, $itemDescription, $itemPrice, $itemQuantity){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/invoices.xml?api_key=".$this->api_key;
  
    $logResource = fopen($this->file, "w+");
    
    $invoice = new SimpleXMLElement("<invoice></invoice>");
    $invoice->addChild('date', $date);
    $invoice->addChild('due_date', $due_date);

    $invoiceCliente = $invoice->addChild('client');
    $invoiceCliente->addChild('name', $clientName);
    $invoiceCliente->addChild('code', $clientCode);

    $invoiceItems = $invoice->addChild('items');
    $invoiceItems->addAttribute('type', 'array');
    
    $invoiceItem = $invoiceItems->addChild('item');
    $invoiceItem->addChild('name', $itemName);
    $invoiceItem->addChild('description', $itemDescription);
    $invoiceItem->addChild('unit_price', $itemPrice);
    $invoiceItem->addChild('quantity', $itemQuantity);
    
    $data = $invoice->asXML();
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
    
    $result = curl_exec($ch);
    
		if(curl_errno($ch)){
			return curl_error($ch);
		}
		
		curl_close($ch);
		
		fclose($logResource); //fecha ficheiro de log
		
		//get client info
		echo $result;
    $createXml = new SimpleXMLElement($result); //parse xml
		$clientid = $createXml->client->id;
		
    $client = new Client();
    $clientGetReturn = $client->Get($clientid);
    $clientGetXml = new SimpleXMLElement($clientGetReturn); //parse xml

    //send email
    $email = $clientGetXml->preferred_contact->email; //get mail
    $invoiceid = $createXml->id;
    $body = "<a href='www.google.com'>Clique aqui</a> para gerar a sua fatura";
    $subject = "Orçamento ".$invoiceid;
    $this->Email($invoiceid, "fabioborges91@gmail.com", "", "", $body, $subject);
    
    return $result;
  }
  
  function Get($invoiceid){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/invoices/".$invoiceid.".xml?api_key=".$this->api_key;
    
    $logResource = fopen($this->file, "w+");

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
  
    $result = curl_exec($ch);
    
    if(curl_errno($ch)){
			return curl_error($ch);
		}
		
    curl_close($ch);
    
    fclose($logResource); //fecha ficheiro de log
    
    return $result;
  }
  
  function Email($invoiceid, $email, $cc, $bcc, $body, $subject){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/invoice/".$invoiceid."/email-invoice.xml?api_key=".$this->api_key;
    $logResource = fopen($this->file, "w+");
    
    $input = new SimpleXMLElement("<message></message>");
    
    $inputCliente = $input->addChild("client");
    $inputCliente->addChild("email", $email);
    $inputCliente->addChild("save", 0);
    
    $input->addChild("subject", $subject);
    $input->addChild("body", $body);
    $input->addChild("cc", $cc);
    $input->addChild("bcc", $bcc);

    $data = $input->asXML();

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
    
    $result = curl_exec($ch);
    
    if(curl_errno($ch)){
			return curl_error($ch);
		}
		
    curl_close($ch);
    
    fclose($logResource); //fecha ficheiro de log
    
    return $result;
  }
  
  function ChangeState($invoiceid, $state, $comment){
    $endpoint = "https://".$this->accountname.".app.invoicexpress.com/invoice/".$invoiceid."/change-state.xml?api_key=".$this->api_key;
  
    $logResource = fopen($this->file, "w+");
    
    $invoice = new SimpleXMLElement("<quote></quote>");
    $invoice->addChild('state', $state);
    $invoice->addChild('comment', $comment);

    $data = $invoice->asXML();
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_TIMEOUT, 40);
    curl_setopt($ch, CURLOPT_VERBOSE, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml; charset=utf-8'));
    curl_setopt($ch, CURLOPT_URL, $endpoint);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_STDERR, $logResource);
    
    $result = curl_exec($ch);
    
		if(curl_errno($ch)){
			return curl_error($ch);
		}
		
		curl_close($ch);
		
		fclose($logResource); //fecha ficheiro de log
  }
}
?>
