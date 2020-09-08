# MyLayeredArchitectureAndroidApplication [WIP]

レイヤードアーキテクチャな Android アプリケーションをで構築する開発基準

### 前提
- Clean Architecture を参考にしているが、厳密には行わない。
- DIフレームワーク(Dagger等)は使用しない。
  - Injector / RepositoryProvider など Util系クラスで事足りと考える。

## [MVVM : Model View ViewModel](https://ja.wikipedia.org/wiki/Model_View_ViewModel)

Android Developers の [Guide to app architecture](https://developer.android.com/jetpack/guide) を参考にして、MVVM を採用する

Model, View, ViewMode を分割し、**関心の分離**、**依存関係** を整理

### View

Ativity/Fragment

1. Ativity
   1. Single
   2. NavGraph で Fragment 遷移
2. Fragment
   1. Viewの操作のみ行う。
   2. ロジックはViewModelで処理
   3. レイアウトしかもたないFragmentもアリ
   4. ViewModelのLiveDataのデータをObserveしてレイアウトを変更
   5. Viewのイベントでロジックが発生する場合はViewModelで行う
   6. Viewや機能が多い場合は、ChildFragment に持たせる
   7. ParentFragment と ChileFragment の Event/Stateのやり取りは ParentViewModel で行う
   8. 画面遷移がある場合は、ParentFragment で、NavGraph を持って ChildFragment を遷移
   9. ViewModelを全てのChileFragmentで共有したい場合 NavGraph Scope で持たせることも可能



### ViewModel
1. Context が絶対に必要でない限り AndroidViewModel は使用しない。
   1. テスタビリティが落ちるため
2. Repository等のインスタンスを内部で生成しない。
3. Factoryを介してRepositoryを与える。


```kotlin
class UsersViewModel internal constructor(userRepository: UserRepository) : ViewModel() {

    val users: LiveData<List<User>> = userRepository.getUsers().asLiveData()

    class Factory(
        private val userRepository: UserRepository
    ) : ViewModelProvider.Factory {
        @Suppress("UNCHECKED_CAST")
        override fun <T : ViewModel> create(modelClass: Class<T>): T {
            return UsersViewModel(userRepository) as T
        }
    }
}

object ViewModelFactoryProvider {
    fun provideUsersViewModelFactory(context: Context): UsersViewModel.Factory {
        return UsersViewModel.Factory(
                RepositoryProvider.getUsersRepository(context)
        )
    }
}

object RepositoryProvider {
    fun getUsersRepository(context: Context): UsersRepository {
        return UsersRepository.getInstance(
                DataBase.getInstance(context.applicationContext)
        )
    }
}

class UsersFragment : Fragment(R.layout.users_fragment) {
    private val viewModel: UsersViewModel by viewModels {
        ViewModelFactoryProvider.provideUsersViewModelFactory(requireContext())
    }
}

```

##### (参考)
- android - AndroidViewModel vs ViewModel - Stack Overflow https://stackoverflow.com/questions/44148966/androidviewmodel-vs-viewmodel
- Flow and LiveData in MVVM architecture - Lukasz Burcon - Medium https://medium.com/@lukaszburcon/flow-and-livedata-in-mvvm-architecture-6f8879b96ce0

#### ViewModelStoreOwner

ViewModel の scope

Activity/Fragment Scope
```
    private val viewModel: MyViewModel by viewModels()
```
Activity Scope
```
    private val viewModel: MyViewModel by activityViewModels()
```
ParentFragment Scope
```
    private val viewModel: MyViewModel by createViewModelLazy(
        MyViewModel::class,
        { requireParentFragment().viewModelStore }
    )
```
NavGraph Scope
```
    private val viewModel: MyViewModel by navGraphViewModels(R.id.my_graph)
```
Custom Scope
```
    private val viewModel: MyViewModel by createViewModelLazy(
        MyViewModel::class,
        { myCustomViewModelStore }
    )
```
CustomViewModelStore 内で ViewModel を create/clear する必要がある


[プログラムで Navigation コンポーネントを操作する  |  Android デベロッパー  |  Android Developers](https://developer.android.com/guide/navigation/navigation-programmatic?hl=ja)

### Model

Layered Architecture 

- UseCase
- Repository
- Datasource
- 