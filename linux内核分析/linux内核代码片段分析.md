# Linux�ں˴���Ƭ�η�����

���Ľ��GCC���ISO C����չ������Linux�ں��еĵ��ʹ���Ƭ�ν��з���������

## 1 ��׼C���Ե�GCC��չ
Linux ��ȫ���� GCC ���µ���ϵ�ṹ�����С�Linux ������ GCC �е����ԣ���Ϊ��չ��ʵ�ָ��๦�ܺ��Ż��������ڲ鿴linuxԴ�����ʱ��������ǳ����GCC��չ�������һ����Ҫ����GCC����ISO C��һЩ��չ��

���ڵ���Ҫ���ݲο��� [Linux �ں��е� GCC ����](https://www.ibm.com/developerworks/cn/linux/l-gcc-hacks/) ��ƪ���¡�

GCC֧�ֺܶ��C���Թ淶�������֧���������ͨ���ٷ��Ķ�Ӧ�汾�ĵ����в鿴����4.8.5�汾Ϊ������ [�ٷ��ĵ�](https://gcc.gnu.org/onlinedocs/gcc-4.8.5/cpp/Invocation.html#Invocation) ��˵����
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

> �Ż�����չ�������ɸ���Ч�Ĵ��롣
  
### 1.1 ��������չ��
��һ����Ҫ����GCC����ISO C�Ĺ����ϵ���չ���൱��������µ��﷨���ԡ�

#### 1.1.1 ���ͷ��֣�
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

#### 1.1.2 ��Χ��
GCC ֧�ַ�Χ���� C ���Ե���෽�涼����ʹ�÷�Χ���ڸ��ӵ������ṹ�У�ͨ������Ƕ�׵�if���ʵ�ֶ��������жϣ�ʹ��GCC��չ�ķ�Χ֮����Լ򻯴��롣

##### 1.1.2.1 ��switch/case�����ʹ�÷�Χ��
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

##### 1.1.2.2 ʹ�÷�Χ���г�ʼ����
�����ڿ��������ʹ�÷�Χָ�����壬��Χ������Ӧ���ڱ����ĳ�ʼ�������򻯴��롣
Դ����λ�ã�
./linux/arch/cris/arch-v32/kernel/smp.c
```gcc-c
/* Vector of locks used for various atomic operations */
spinlock_t cris_atomic_locks[] = { [0 ... LOCK_COUNT - 1] = SPIN_LOCK_UNLOCKED};
```
����������У�Ϊ spinlock_t ����һ����СΪ LOCK_COUNT �����顣�����ÿ��Ԫ�س�ʼ��Ϊ SPIN_LOCK_UNLOCKED ֵ��
��Χ��֧�ָ����ӵĳ�ʼ�������磬���´���ָ�������м����ӷ�Χ�ĳ�ʼֵ��
```gcc-c
int widths[] = { [0 ... 9] = 1, [10 ... 99] = 2, [100] = 3 };
```

#### 1.1.3 �㳤�ȵ����飺
�� C ��׼�У����붨������һ������Ԫ�ء��������������ʹ������Ƹ��ӻ������ǣ�GCC ֧���㳤������ĸ������ڽṹ�����������á���������� ISO C99 �����������Ա���ƣ�����ʹ�ò�ͬ���﷨��
Դ����λ�ã�./linux/drivers/ieee1394/raw1394-private.h
```gcc-c
struct iso_block_store {
        atomic_t refcount;
        size_t data_size;
        quadlet_t data[0];
};
```
����ṹ�嶨���У��ڽṹ��ĩβ����һ��û�г�Ա������data��������ṹ�е�Ԫ�����ýṹʵ����������ŵ��ڴ档����Ҫ�����ɱ�������Աʱ��������Ժ����á�

#### 1.1.4 �жϵ��õ�ַ��


### 1.2 ��������չ��
#### 1.2.1
##### 1.2.1.1




## 2 Linux kernel������������

### 2.1 ����
��linux�ں˵����������У�����������Ҫ�����á��ں��е�����ʵ��λ�ڣ�
```shell
../include/linux/list.h
```
����Ļ����ṹ�ܼ򵥣�
```shell

```












