# ����Node.js�Ĵ��ڵ�������
**˵��**���ó���ͨ��node.js����ʵ�ֶԴ��ڽ��ж�д������node.js�Ĵ��ڳ��򿪷������밲װnode.js�µĴ��ڿ����ؼ���serialport����
**��װ**�� `npm install serialport`
    ![���ڵ������ֽ���](/2015-10-09_114116.png)
***
## 1.��������
    function open_serial() {
         var serialPort = new SerialPort.SerialPort(com_num, {
             baudrate: baudrate,  //����������
             databits: databits,  //����λ
             parity: parity,  //У��λ
             stopbits: stopbits //ֹͣλ
             //  parser: SerialPort.parsers.readline("\n")  //�����ܵ��÷������ԣ��������ͻ���ֽ������ݱ��벻����
         });
         data_to_web('�����Ѿ���');
         sp = serialPort;
    }
  **˵��**��ͨ���÷����������ڡ�ע�ⴴ�����ڵ�ʱ�򴮿ں��Ǳ�Ҫ����������ͻᱨ�����޷������˿ڡ�
***
## 2.���˿�����
         function serial_read() {
               sp.on('data', function (info) {
               console.log('data_change:' + iconv.decode(info, rec_setting));
               data_to_web(iconv.decode(info, rec_setting));
               data_to_web('���������ֽڳ��ȣ�' + info.length);
           });
           }
**˵��**��ͨ���÷�����ȡ�������ݡ�
***
## 3.д�˿�����
    function serial_write() {
         var buf_once = new Buffer(write_data, send_setting);
         sp.write(buf_once, function (err, results) {
             if (err) {
                 console.log('err ' + err);
             }
             else {
                 data_to_web('�������ݣ�' + write_data.toLocaleUpperCase());
                 data_to_web('���������ֽڳ��ȣ� ' + results);
                 console.log('��������:' + write_data.toLocaleUpperCase());
                 console.log('���������ֽڳ��ȣ� ' + results);  //����ȥ�������ֽڳ���
             }
         });
         if (auto_send_time != 0) {
             var autoSend = setInterval(function () {
                 if (serial_flag != 'close') {
                     var buf = new Buffer(write_data, send_setting);
                     sp.write(buf, function (err, results) {
                         if (err) {
                             console.log('err ' + err);
                         }
                         else {
                             data_to_web('�������ݣ�' + write_data.toLocaleUpperCase());
                             data_to_web('���������ֽڳ��ȣ� ' + results);
                             console.log('��������:' + write_data.toLocaleUpperCase());
                             console.log('���������ֽڳ��ȣ� ' + results);  //����ȥ�������ֽڳ���
                         }
                     })
                 }
                 else {
                     clearInterval(autoSend);
                 }
             }, auto_send_time);
         }
     }
**˵��**��ͨ���÷����򴮿�д�����ݡ���Ϊ��ʵ�ʴ��ڵ����д��ڵ��η��ͺͶ�ʱ������������������Ҳ������������������֡���Ȼ�������ǵ��·��Ͱ�ť��ʱ�򣬱���������һ�����ݷ����������������������Ĵ�����ʾ�����ø÷�����ʱ�������Ȼ��ȷ���һ�����ݡ��������ĺô��Ǳ�����ʱ���󣬱�����Ҫ���2s��ʱ�������ݣ���ô�����Ͱ�ť�����ɣ�2s��ŻῪʼ�������ݡ�
***
## 4.���ݷ��ͷ�ʽ�ж�
    function send_juge() {

     //�����ֶ���������
     if (command != "" && auto_send == "auto_send_no") //���������ֶ�����
     {
         serial_read_write(command, 0);
     }
     //�����Զ�����
     if (command != "" && auto_send == "auto_send_yes") //��ʱ�Զ�����
     {
         serial_read_write(command, auto_time);
     }
    }
 ***
 ## 5.����
 �����滹�������ķ������߼��жϣ����ﲻһһϸ˵���ڳ������涼����Ӧ��ע�͡���web�������˶��ajax����ҳ����Ϣ����̨������¼�������ֻ����һ��ʾ��˵����
 
    $(function()
          {
              $('#set_change').click(function()
              {
                  rec_setting = $("#rec_setting").find('option:selected').val();//��ȡ���ձ���
                  send_setting = $("#send_setting").find('option:selected').val();//��ȡ���ͱ���
                  var encode_set = {
                      rec_setting: rec_setting,
                      send_setting: send_setting
                  };
                  var JSON_encode = JSON.stringify(encode_set);  //��js����ת����json��ʽ����
                  $.ajax({
                      type: 'post',
                      url: "command_1.1.html",
                      data: JSON_encode,
                      async: true,
                      // dataType: 'json',
                      success: function (data) {
                          // alert(data);
                      },
                      error: function (XMLHttpRequest, textStatus, errorThrown) {
                          // alert("Save error!" + XMLHttpRequest.status + " "  + XMLHttpRequest.readyState);
                          alert("ָ���ʧ�ܣ�����������Ƿ�����������")
                      }
                  });
                  connect_toserver.emit('encode_setting', encode_set);
              })
          });
**˵��**��������������ͱ������õ���̨����