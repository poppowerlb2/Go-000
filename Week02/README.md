## 第二周作业
我们在数据库操作的时候，比如 `dao` 层中当遇到一个 `sql.ErrNoRows` 的时候，是否应该 `Wrap` 这个 `error`，抛给上层。为什么？应该怎么做请写出代码

答：报错合适。dao层只是用于获取数据，对于数据的处理应该放在service层或者更上层去做，所以在dao层应该如实反映操作数据的情况。

## 代码实现
```go
    // DAO层
    var (
    	ErrDataNotFound = errors.New("data not found")
    )

    type User struct {
        Id uint64 `json:"Id"`
        Name string `json:"name"`
    }

    // DAO 层基本接口
    type Dao interface {
        Get(id uint64) interface{}
				......
    }

    type UserDao struct {}

    // Dao层获取到底层错误，使用errors的Wrap进行包装
    fuc(user *UserDao) Get(id uint64) (*User, error) {
        user := User{}
        err := db.Where("id = ?", id).Find(user).Error

        if errors.Is(err,sql.ErrNoRows) {
            retrun nil, errors.Wrap(err, fmt.Sprintf("can't find user by user id: %v", id))
        }
				// TODO Wrap其它错误
        return &user, nil
    }
```
 
```go
    // Service层

    // Service层如获取到错误继续往上层抛
    type UserService struct {}

    func (s *UserService) FindUserByID(userID int) (*model.User, error) {
        return dao.FindUserByID(userID)
    }
```
