# LoongCore
>��õ������˽⣬����ʵ��

������ߣ, ��ͨ����Ԫ�������˽�MVVM��ܵ��ں�

## 01.Abstract�͵�Ԫ���Գ�����

1. ����ObservableObject��
�����������ǽ���ViewModel�Ļ��࣬��������û��ʵ�ʺ��壬Ӧ�ö�Ϊ�����࣬���˴���������
```c#
    public class ObservableObject : INotifyPropertyChanged
    {
              public event PropertyChangedEventHandler PropertyChanged;
 
    }
```
2. �½���Ԫ����
![01.Is Abstract](Figures/01.IsAbstract.png)

3. �ڵ�Ԫ������Ŀ��ȷ��ObservableObjectΪ������
```c#
    [TestClass]
    public class ObservableObject_Test
    {
        [TestMethod]
        public void IsAbstract() {

            var type = typeof(ObservableObject);
            Assert.IsTrue(type.IsAbstract);
        }
    }
```

4. �ڲ�����Դ�����������в���   
![01.Is Abstract Test](Figures/01.IsAbstractTest.png)

5. �޸�ObservableObject�����ͨ��
```c# 
    public abstract class ObservableObject : INotifyPropertyChanged
    {
              public event PropertyChangedEventHandler PropertyChanged;
 
    } 
```