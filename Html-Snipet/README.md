## Generate Qr Code 
<code>
  <style>
    .print-format {
      padding: 0px;
    }
  
    @media screen {
      .print-format {
        padding: 0in;
      }
    }
  
  </style>
  
  <div style="position: relative; top:0.25cm">
    <div style="width:6.35cm;height:3.81cm;">
      <img alt='' src='https://barcode.tec-it.com/barcode.ashx?data={{ doc.item_name }} SN%3A%20{{ doc.name }}&code=QRCode&translate-esc=on' />
      <br>
      Item:{{ doc.item_code  }}
      <br>
      SN:{{ doc.serial_no  }}
    </div>
  </div>

</code>
