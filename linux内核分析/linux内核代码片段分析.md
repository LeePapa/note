���������Ҫ�漰��linux�ں�Դ��������صĴ���Ƭ�Σ�Ȼ����з���������

 Linux ��ȫ���� GCC ���µ���ϵ�ṹ�����С�Linux ������ GCC �е����ԣ���Ϊ��չ��ʵ�ָ��๦�ܺ��Ż��������ڲ鿴linuxԴ�����ʱ��������ǳ����GCC��չ�����

# 1 ��׼C���Ե�GCC��չ
GCC֧�ֺܶ��C���Թ淶�������֧���������ͨ���ٷ��Ķ�Ӧ�汾�ĵ����в鿴����4.8.5�汾Ϊ����[�ٷ��ĵ�](https://gcc.gnu.org/onlinedocs/gcc-4.8.5/cpp/Invocation.html#Invocation)����˵����
```shell
-std=standard
-ansi
	Specify the standard to which the code should conform. Currently CPP knows about C and C++ standards; others may be added in the future.
	standard may be one of:

	c90
	c89
	iso9899:1990
		The ISO C standard from 1990. ��c90�� is the customary shorthand for this version of the standard.
		The -ansi option is equivalent to -std=c90. 

	iso9899:199409
		The 1990 C standard, as amended in 1994. 
	iso9899:1999
	c99
	iso9899:199x
	c9x
		The revised ISO C standard, published in December 1999. Before publication, this was known as C9X. 
	iso9899:2011
	c11
	c1x
		The revised ISO C standard, published in December 2011. Before publication, this was known as C1X. 
	gnu90
	gnu89
		The 1990 C standard plus GNU extensions. This is the default. 
	gnu99
	gnu9x
		The 1999 C standard plus GNU extensions. 
	gnu11
	gnu1x
		The 2011 C standard plus GNU extensions. 
	c++98
		The 1998 ISO C++ standard plus amendments. 
	gnu++98
		The same as -std=c++98 plus GNU extensions. This is the default for C++ code.
```
���Կ�����ǰ�汾�ı�����֧�ֵ����о���汾��Ϣ��

���ļ���C����ʹ�� ISO C99 ��׼��GCC���ڱ�׼C��������չ����Ҫ��������������ݣ�
> �����ԣ���չ�ṩ�¹��ܡ�
  �Ż�����չ�������ɸ���Ч�Ĵ��롣
  
���Ľ��linuxԴ����Գ��õ���չ��Ͻ��з�����

## 1.1 ���ͷ��֣�
GCC����ͨ������������ʶ�����͡����ֲ���֧�ַ��ͱ�̡�ʹ��typeof����һ�����ͺ꣺
Դ����λ�ã� ./linux/include/linux/kernel.h
```gcc-c
#define min(x, y)                                 /
({                                                /
    typeof(x) _min1 = (x);                        /
    typeof(y) _min2 = (y);                        /
    (void) (&_min1 == &_min2);                    /
    _min1 < _min2 ? _min1 : _min2;                /
}) 
```
������е�"(void) (&_min1 == &_min2);"��δ�����Ϊ�˼���Ƿ���ͬ���͵ı������бȽϣ����û����һ���жϣ����޷��������¾��棺
```shell
comparison of distinct pointer types lacks a cast
```

## 1.2 ��Χ��
GCC ֧�ַ�Χ���� C ���Ե���෽�涼����ʹ�÷�Χ���ڸ��ӵ������ṹ�У�ͨ������Ƕ�׵�if���ʵ�ֶ��������жϣ�ʹ��GCC��չ�ķ�Χ֮����Լ򻯴��롣
������ switch/case ���е� case �����ʹ�÷�Χ��ͬ�£�ʹ�� switch/case Ҳ����ͨ��ʹ����ת��ʵ�ֽ��б������Ż���
Դ����λ�ã� ./linux/drivers/scsi/sd.c
```gcc-c
static int sd_major(int major_idx)
{
	switch (major_idx) {
	case 0:
		return SCSI_DISK0_MAJOR;
	case 1 ... 7:
		return SCSI_DISK1_MAJOR + major_idx - 1;
	case 8 ... 15:
		return SCSI_DISK8_MAJOR + major_idx - 8;
	default:
		BUG();
		return 0;	/* shut up gcc */
	}
}
```
����"case 1 ... 7:"ʹ������������ʾ��1��7��������Χ��������һ�������жϡ�
