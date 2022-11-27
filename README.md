# ToDoList

## 🍎 작동 화면

| 할 일 추가 | 할 일 삭제 |
|:-:|:-:|
| ![](https://i.imgur.com/0gg5FVW.gif)| ![](https://i.imgur.com/3Qk0ajr.gif) |
| 할 일 재배치 | 할 일 저장 |
![](https://i.imgur.com/XDTyxkp.gif) | ![](https://i.imgur.com/HIBEUFf.gif) |

## 🍎 TableView 만들기
- Navigation Controller 추가
    - Table View도 같이 생성됨
    - 같이 생성될 때에는 Prototype Cells가 1이지만 만약 기본 View Controller에 table view를 넣는다면 0으로 세팅되어 있으니 필요한 수만큼 만들어주자
- Table View Cell 스타일
    - 기본 -> Custom
    - 사용자의 요구에 맞게 사용

## 🍎 TableView를 유용하게 만들어주는 메서드 정리
- [TableView DataSource / Delegate Protocol 정리](https://github.com/KayAhn0126/iOS-Study/tree/main/GrammarAndKnowledge/TableViewProtocols)

## 🍎 Alert 정리
- [ToDoList 프로젝트에서 Alert 사용 정리](https://github.com/KayAhn0126/iOS-Study/tree/main/UI/UIAlertController/UIAlertToDoListExample)

## 🍎 [userDefaults에서 구조체 인스턴스 사용하기 & 리서치](https://github.com/KayAhn0126/iOS-Study/tree/main/GrammarAndKnowledge/HandlingObjectInUserDefaults)
- saveTasks()는 userDefaults에 데이터를 저장하는 메서드
- loadTasks()는 userDefaults에서 데이터를 가져오는 메서드
- Property Object Type으로 userDefaults에 데이터를 저장하는 코드
![](https://i.imgur.com/WJmb0XR.png)
- 데이터를 저장하기도 불러와서 사용하기도 복잡해서 연습할 겸, 인스턴스화 시킨 후 사용하는 코드를 작성해보았다.
![](https://i.imgur.com/XG9KBZ0.png)


## 🍎 UITableViewDataSource내 tableView(_:commit:, _:forRowAt:) 메서드
- 편집모드에서 삭제버튼이 눌렸을떄 삭제 버튼이 눌려진 셀이 어떤 셀인지 알려주는 메서드
- 그래서 이 메서드에서 삭제 버튼이 눌려진 행이 테이블 뷰에서 삭제되게 구현했다.
```swift
func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
    self.tasks.remove(at: indexPath.row) // 데이터에서 삭제
    tableView.deleteRows(at: [indexPath], with: .automatic) // 테이블 뷰에서도 삭제
    // deleteRows에 인자로 여러개의 indexPath를 받을수 있게 배열 형태이다!
        
    if self.tasks.isEmpty {
        self.doneButtonTap()
    }
}
```
- 이 메서드를 구현하면 편집 모드에 들어가지 않아도 셀을 오른쪽에서 왼쪽으로 스와이프 하면 셀을 삭제할 수 있게 만들어준다.
- 사실 tapEditButton() 인스턴스 메서드에서 구현한 setEditing()메서드의 역할은 아래 사진처럼 셀의 좌측에 빨간 삭제 버튼을 추가만 하는것이다.
![](https://i.imgur.com/YS2Iv0L.png)
- 빨간 삭제 버튼이 눌렸을때 셀의 우측에서 Delete 버튼이 나오게 하려면 위의 commit, forRowAt메서드를 구현 해주어야 한다.

## 🍎 UITableViewDataSource내 tableView(_:canMoveRowAt:) 메서드
- 이 메서드는 **table view가 editing 모드에 들어갈 때(즉, setEditing(true)일때) 실행되는 메서드**이다.
- 반환형이 Bool 타입이라 만약 false를 반환시킨다면 위치를 바꿀수 없게 editing 모드에서 셀의 오른쪽에 표시되는 삼선을 보이지 않게 한다.
- 공식문서에 따르면, 이 메서드는 지정된 row에 대하여 이동 컨트롤(삼선)이 보이지 않게 할 수 있다. 만약 tableView(_:moveRowAt:to:) 메서드를 구현하면 자동으로 이동 컨트롤(삼선)을 보여주고 이동까지 가능하게 만들어 준다.라고 써있다.

## 🍎 UITableViewDataSource내 tableView(_:moveRowAt:to:) 메서드
- **셀이 이동했을때 원래 위치(sourceIndexPath)와 이동된 위치(destinationIndexPath)를 매개변수로 받아와서 사용 가능하게 해준다.**
- 현재 프로젝트에서는 만약 tableView에서 셀의 위치가 옮겨졌다면, 해당 데이터의 순서도 바꿔주고 있다.
```swift
func tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
    var tasks = self.tasks
    let task = tasks[sourceIndexPath.row]
    tasks.remove(at: sourceIndexPath.row)
    tasks.insert(task, at: destinationIndexPath.row)
    self.tasks = tasks
}
```

## 🍎 tableView(_:canMoveRowAt:)와 tableView(_:moveRowAt:to:)의 조합
- tableView(_:canMoveRowAt:) 구현했다는 의미는 구현과 동시에 값을 true로 주었다는 의미.
- Bold로 되어있는 항목이 가장 많이 쓰이는 방식.
- tableView(_:canMoveRowAt:) 미구현 + tableView(_:moveRowAt:to:) 미구현
    - 이동 컨트롤(삼선) 없음 + 셀 이동 불가 + 셀이 어디서 어디로 이동했는지 알 수 없음
- tableView(_:canMoveRowAt:) 미구현 + tableView(_:moveRowAt:to:) 구현
    - 이동 컨트롤(삼선) 있음 + 셀 이동 가능 + 셀이 어디서 어디로 이동했는지 알 수 있음
- tableView(_:canMoveRowAt:) 구현 + tableView(_:moveRowAt:to:) 미구현
    - 이동 컨트롤(삼선) 있음 + 셀 이동 가능 + 셀이 어디서 어디로 이동했는지 알 수 없음
- **tableView(_:canMoveRowAt:) 구현 + tableView(_:moveRowAt:to:)** 
    - **이동 컨트롤(삼선) 있음 + 셀 이동 가능 + 셀이 어디서 어디로 이동했는지 알 수 있음**

## 🍎 UITableViewDelegate내 tableView(_:didSelectRowAt:) 메서드 내부 구현 살펴보기
```swift
extension ViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        var task = self.tasks[indexPath.row]
        task.done.toggle()
        self.tasks[indexPath.row] = task
        self.tableView.reloadRows(at: [indexPath], with: .automatic)
    }
}
```
- 1. 선택된 행의 row를 가ㅣㅈ고 tasks 배열에서 요소을 가져온다.
- 2. 요소의 done 프로퍼티를 반대로 바꿔준다.
- 3. 프로퍼티 값이 바뀐 요소를 다시 tasks 배열의 원래 위치에 넣어준다.
- 4. 해당 행만 새로고침해준다.
    - tableView 전체를 새로고침 한다면 많은 비용이 들어, reloadRows메서드로 바뀐 부분만 새로고침 하게 구현.

## 🍎 나중에 시도하면 좋은 개념
- [테이블 뷰(UITableView)에서 setEditing이 true일때만 삭제 등 작업 가능하게 하기](http://yoonbumtae.com/?p=4515)

## 🍎 Citation
- [UITableViewDataSource내 canMoveRowAt 공식문서](https://developer.apple.com/documentation/uikit/uitableviewdatasource/1614927-tableview)
