# inject
--
    import "github.com/shima-park/inject"

Package inject provides utilities for mapping and injecting dependencies in
various ways.

## Usage

#### 说明
原始分支基于reflect.Type作为key，reflect.Value作为value进行存储，并以此注入
本分支在原始基础上进行了改造，Invoke不再支持任意类型,而只支持传递结构体/结构体指针类型

类型依赖注入
``` go
    type TestStruct struct {
	    Dep1 string        `inject`
	    Dep2 SpecialString `inject`
	    Dep3 string        `inject:"t"`
        Dep4 string
    }

    injector.Map("a dep", "Dep1")
    injector.MapTo("another dep", "Dep2", (*SpecialString)(nil))
    injector.Map("a dep3", "t")

	s := TestStruct{}
	injector.Apply(&s)


    fmt.Println(s.Dep1) // "a dep"
    fmt.Println(s.Dep2) // "another dep"
    fmt.Println(s.Dep3) // "a dep3"
    fmt.Println(s.Dep4) // ""
```

方法依赖注入(只支持传递结构体/结构体指针类型)
``` go
    type TestStruct struct {
	    Dep1 string        `inject:"t" json:"-"`
	    Dep2 SpecialString `inject`
	    Dep3 string
    }

    injector.Invoke(func(s *TestStruct, s2 TestStruct) {
		fmt.Println(s.Dep1) // "a dep"
        fmt.Println(s.Dep2) // "another dep"
        fmt.Println(s.Dep3) // ""

        fmt.Println(s2.Dep1) // "a dep"
        fmt.Println(s2.Dep2) // "another dep"
        fmt.Println(s2.Dep3) // ""
	})
```
