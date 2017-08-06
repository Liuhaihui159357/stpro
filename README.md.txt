# stpro һ������tcpЭ��ʵ�ֵļ��ͨ�ſ��
# a skeleton for communication based on TCP

## ����

* ����go������ʹ��
* ʵ����crcУ�飬��֤���ݴ��������������ȷ��
* ���÷�ʽ������

## ���ٿ�ʼ

### 1. ����

```
    import "stpro"
    
```
### 2. server ��

```
    /** ����������
        1 ������������struct�����ݽṹ���������Pmap��Phost����
          �ֶΣ�����PhostΪ�����ip+portƴ�ӵ��ַ�����PmapΪ�Զ�
          �����ݰ����������ݰ����Ƶ�ӳ�䡣
        2 ʵ��������Ϊ�ֶθ�ֵ��ʵ�ֶ�Ӧ�Ѷ���`������`�����ݰ���
          ��������������Ϊ"P[������]",��type���Ĵ�����ΪPtype
          ���������붨�����ݴ����߼�,���������Ϊ[]byte���͡�
        3 stpro.New()����ʵ�����Ķ������ޱ��������˿�ʼ������
          ����������������߼��������ݰ���������Ӧ���ݡ�
    **/
    package main

    import (
        "fmt"
	    "stpro"
    )

    type Server struct {
	    Phost string
	    Pmap  map[uint8]string
    }

    func (m Server) Ptype(in []byte) (out []byte) {
	    fmt.Printf("�ͻ��˷���type��:%s\n", in)
        /** process... **/
    	bytes := []byte("hello1")
	    return bytes
    }

    func (m Server) Pname(in []byte) (out []byte) {
	    fmt.Printf("�ͻ��˷���name��:%s\n", in)
        /** process... **/
        bytes := []byte("hello2")
	    return bytes
    }

    func main() {
	    m := Model{
		    Phost: ":9091",
		    Pmap:  make(map[uint8]string),
	    }
    	m.Pmap[0x01] = "type"
    	m.Pmap[0x02] = "name"
    	err := stpro.New(m)
    	if err != nil {
	    	fmt.Println(err)
	    }
   }

```

### 3.client��

```
    /**
        ������ͻ���
        1 ���ݽṹͬ����ˡ�
        2 P[type]�����Ƿ��Ͷ�Ӧ������յ���Ӧ���ݵĴ�������
        3 ʵ�������󣬲�����Send(type byte, content []byte)��
          ���������ݵ��ͻ��ˣ����յ������ݺ���Զ�����������
          �巽������
    **/
    package main

    import (
        "fmt"
	    "stpro"
    )

    type Client struct {
    	Phost string
	    Pmap  map[byte]string
    }

    func (c Client) Ptype(in []byte) {
	    fmt.Printf("�յ���type���Ļظ�:%s\n", in)
    }

    func (c Client) Pname(in []byte) {
	    fmt.Printf("�յ���name���Ļظ�:%s\n", in)
    }

    func main() {
	    client, err := stpro.NewClient(Client{
		    Phost: "192.168.1.106:9091",
		    Pmap: map[byte]string{
		    	0x01: "type",
			    0x02: "name",
		    },
	    })

	    if err != nil {
		    fmt.Println(err)
		    return
	    }

	    err = client.Send(0x02, []byte("jintianzhenhao"))
	    if err != nil {
		    fmt.Println(err)
		    return
	    }

	    err = client.Send(0x01, []byte("jintianzhenhao3333"))
	    if err != nil {
		    fmt.Println(err)
		    return
	    }
    }
```