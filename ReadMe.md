# LoongCore �������MVVM
>��õ������˽⣬����ʵ��

������ߣ, ��ͨ����Ԫ�������˽�MVVM��ܵ��ں�

## 12.Abstract�͵�Ԫ���Գ�����

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

## 13.PropertyChanged���Ըı�ʱ������ʲô

1. ```ObservableObject.cs```���Ըı�ʱ�����¼��ĵײ㷽��
```c#
        // TODO: 13-1 �������Ըı��¼��ķ���
        /// <summary>
        ///     �������Ըı��¼�
        /// </summary>
        ///     <param name="propertyName">�����ı�����Ե�����</param>
        /// <remarks>
        ///     ?. ������������˸�ViewModel���ˡ���Ƭ���Ż����������ⲿ���˶�����PropertyChanged
        ///     û����������ǿ��Եģ���������ܵ�Ӳ����дpropertyName
        /// </remarks>
        protected void RaisePropertyChanged([CallerMemberName] string propertyName = null)
            => PropertyChanged?.Invoke
                                (
                                    this,
                                    new PropertyChangedEventArgs(propertyName)
                                );
```
2. ```ObservableObject.cs```�������������������õ�ֵȷʵΪ��ֵʱ�����RaisePropertyChanged
```c#
        // TODO: 13-2 ����������
        /// <summary>
        /// �����µ�����ֵ,����ǡ�����¡�������<seealso cref="RaisePropertyChanged(string)"/>
        /// </summary>
        ///     <typeparam name="T">Ŀ�����Ե�����</typeparam>
        ///     <param name="target">Ŀ������</param>
        ///     <param name="value">�������µ�ֵ</param>
        ///     <param name="propertyName">[��Ҫ����]Ŀ�����Ե����ƣ��Զ��ƶ�</param>
        /// <returns>[true]Ŀ�������ѱ����£�</returns>
        protected bool SetProperty<T>
            (
                    ref T target, // Ŀ������
                    T value,      // ���¡�ֵ
                    [CallerMemberName] string propertyName = null
            ) {
            if (EqualityComparer<T>.Default.Equals(target, value))
                return false;

            target = value;
            RaisePropertyChanged(propertyName);
            return true;
        }
```
3. ��Ԫ����
```c#
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
// TODO: 13-3 ����Logger��¼��
using LoongEgg.LoongLogger;

namespace LoongEgg.LoongCore.Test
{
    [TestClass]
    public class ObservableObject_Test
    {
        // TODO: 13-4 ���Գ�ʼ��
        /// <summary>
        /// ��ʼ�����ԣ��������в��Է���ǰ����
        /// </summary>
        /// <remarks>
        ///     LoongEgg.LoongLogger���ҵ�һ����Դ��Ŀ������Բ�ʹ��
        /// </remarks>
        [TestInitialize]
        public void EnabledLogger() {
            LoggerManager.Enable(LoggerType.File, LoggerLevel.Debug);
        }
        
        /// <summary>
        /// ������ȷ��
        /// </summary>
        [TestMethod]
        public void IsAbstract() {
            var type = typeof(ObservableObject);
            Assert.IsTrue(type.IsAbstract);
        }

        // TODO: 13-5 ���һ��������
        /// <summary>
        /// <see cref="ObservableObject"/>��һ����������
        /// </summary>
        public class ObservableObjectSample : ObservableObject
        {
            /// <summary>
            /// ��������
            /// </summary>
            public int PropertySample {
                get => _PropertySample;
                set => SetProperty(ref _PropertySample, value);
            }
            /// <summary>
            /// �����ֶ�
            /// </summary>
            private int _PropertySample;

        }

        // TODO: 13-6 ���Ըı�ʱ�ᷢ��ʲô
        /// <summary>
        /// ���Ըı䣬�һ������¼�ȷ��
        /// </summary>
        [TestMethod] 
        public void CanPropertyChangedRaised() {
            bool isPropertyChangeRaised = false;// �¼��������

            // ��ʼ��һ���������
            ObservableObjectSample sample = new ObservableObjectSample();

            // ע�����Ըı�ʱ�Ĵ����¼�
            sample.PropertyChanged += (s, args) =>
                                        {
                                            isPropertyChangeRaised = true;
                                            LoggerManager.WriteDebug($"PropertyName:{args.PropertyName}");
                                        };

            // �ı�����
            sample.PropertySample = 666;
            Assert.IsTrue(isPropertyChangeRaised);
            Assert.AreEqual(sample.PropertySample, 666);
        }

        // TODO: 13-7 ������Ի���
        /// <summary>
        /// �����в�����ɺ���ã�ע��LoggerManager
        /// </summary>
        [TestCleanup]
        public void DisableLogger() {
            LoggerManager.WriteDebug("LoggerManager is clean up...");
            LoggerManager.Disable();
        }

    }
}

```

## 14.����ȷ��ʱ������֪ͨ

1. ���Բ������ı��ʱ��Ҫ�����¼�
```c#
        // TODO: 14-1 ������ֵ�����ڵ�ǰֵʱ������֪ͨ
        /// <summary>
        /// ������ֵ�����ڵ�ǰֵʱ������֪ͨ
        /// </summary>
        public void WhenPropertyEqualsOldValue_NotRaised() {
            bool isPropertyChangeRaised = false;// �¼��������

            // ��ʼ��һ���������
            // ע�����︳��һ����ʼֵ
            ObservableObjectSample sample = new ObservableObjectSample { PropertySample = 666};

            // ע�����Ըı�ʱ�Ĵ����¼�
            sample.PropertyChanged += (s, args) =>
                                        {
                                            isPropertyChangeRaised = true;
                                            LoggerManager.WriteDebug( 
                                                $"Event is raised by PropertyName={args.PropertyName}, value={sample.PropertySample}");
                                        };

            // �ı�����
            sample.PropertySample = 666;
            Assert.IsFalse(isPropertyChangeRaised); // ע�����������Flase
            Assert.AreEqual(sample.PropertySample, 666);
        }

```
2. ����ViewModelBase.cs
```c#
    // TODO: 14-2 ���ViewModel�Ļ���
    /// <summary>
    /// ViewModel�Ǽ̳��ڴ�
    /// </summary>
    public abstract class ViewModelBase : ObservableObject { }
```
3. ���������˵����Ըı��¼����ɱ�����������

![14.People](Figures/14.People.png)  
��```ViewModelBase_Test.cs```���Ӳ�����
```c#
public class People: ViewModelBase
        {

            public string FamilyName {
                get => _FamilyName;
                set {
                    if (SetProperty(ref _FamilyName, value))
                        RaisePropertyChanged("FullName");
                }
            }
            private string _FamilyName = "[NotDefined]";


            public string LastName {
                get => _LastName;
                set {
                    if (SetProperty(ref _LastName, value))
                        RaisePropertyChanged(nameof(FullName));
                }
            }
            private string _LastName = "[Unknown]";

            public string FullName => $"{FamilyName} - {LastName}";
        }
```
4.�����ĵ�Ԫ����
```c#
using System;
using LoongEgg.LoongLogger;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace LoongEgg.LoongCore.Test
{
    [TestClass]
    public class ViewModelBase_Test
    {   /// <summary>
        /// ��ʼ�����ԣ��������в��Է���ǰ����
        /// </summary>
        /// <remarks>
        ///     LoongEgg.LoongLogger���ҵ�һ����Դ��Ŀ������Բ�ʹ��
        /// </remarks>
        [TestInitialize]
        public void EnabledLogger() {
            LoggerManager.Enable(LoggerType.File, LoggerLevel.Debug);
            LoggerManager.WriteDebug("Test initialized ok ....");
        }
        
        // TODO: 14-3��Ʋ�����People
        public class People: ViewModelBase
        {

            public string FamilyName {
                get => _FamilyName;
                set {
                    if (SetProperty(ref _FamilyName, value))
                        RaisePropertyChanged("FullName");
                }
            }
            private string _FamilyName = "[NotDefined]";


            public string LastName {
                get => _LastName;
                set {
                    if (SetProperty(ref _LastName, value))
                        RaisePropertyChanged(nameof(FullName));
                }
            }
            private string _LastName = "[Unknown]";

            public string FullName => $"{FamilyName} - {LastName}";
        }

        // TODO: 14-4 ������ǿ���������Ըı��¼�
        [TestMethod]
        public void CanRaisedByOtherProperty() {

            People people = new People();
            bool isRaised = false;
            people.PropertyChanged += (s, e) =>
                                        {
                                            isRaised = true;
                                            if(e.PropertyName == "FullName") {
                                                LoongLogger.LoggerManager.WriteDebug($"FullName is changed to -> {people.FullName}");
                                            }
                                        };

            people.FamilyName = "Alpha";
            people.LastName = "Jet";
            Assert.IsTrue(isRaised);
        }
         
        /// <summary>
        /// �����в�����ɺ���ã�ע��LoggerManager
        /// </summary>
        [TestCleanup]
        public void DisableLogger() {
            LoggerManager.WriteDebug("LoggerManager is clean up...");
            LoggerManager.Disable();
        }
    }
}

```