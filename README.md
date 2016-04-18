# cpp-type-index
Dispatching types using injected base class

--

Appently it is possible to dispatch type information from a hierachy of classes by putting a virtual (not pure) method in the base class that returns the type index. So that the following works with `BaseVirtual` as base class.
```c++
  #include <iostream>
  #include <map>
  #include <memory>
  #include <string>
  #include <typeindex>
  
  struct Base
  {
    std::type_index get_type_index( ) const
    {
      return std::type_index( typeid(*this) );
    }
  };
  
  struct BaseVirtual
  {
    virtual std::type_index get_type_index( ) const
    {
      return std::type_index( typeid(*this) );
    }
  };
  
  template<typename BaseType>
  struct A : public BaseType
  {
  };
  
  template<typename BaseType>
  struct B : public BaseType
  {
  };
  
  template<typename BaseType>
  struct C : public BaseType
  {
  };
  
  int main( int argc,
            char **argv )
  {
    std::map<std::type_index, std::string> typeToStringMap = { 
      { std::type_index( typeid(Base) ), "Base" }, 
      { std::type_index( typeid(A<Base> ) ), "ABase" }, 
      { std::type_index( typeid(B<Base> ) ), "BBase" }, 
      { std::type_index( typeid(C<Base> ) ), "CBase" }, 
      { std::type_index( typeid(BaseVirtual) ), "BaseVirtual" }, 
      { std::type_index( typeid(A<BaseVirtual> ) ), "ABaseVirtual" }, 
      { std::type_index( typeid(B<BaseVirtual> ) ), "BBaseVirtual" }, 
      { std::type_index( typeid(C<BaseVirtual> ) ), "CBaseVirtual" } 
    };
  
    A<Base> a;
    B<Base> b;
    C<Base> c;
  
    A<BaseVirtual> av;
    B<BaseVirtual> bv;
    C<BaseVirtual> cv;
  
    auto asp = std::make_shared<A<Base>>( );
    auto bsp = std::make_shared<B<Base>>( );
    auto csp = std::make_shared<C<Base>>( );
  
    auto avsp = std::make_shared<A<BaseVirtual>>( );
    auto bvsp = std::make_shared<B<BaseVirtual>>( );
    auto cvsp = std::make_shared<C<BaseVirtual>>( );
  
    A<Base>* ap = new A<Base>;
    B<Base>* bp = new B<Base>;
    C<Base>* cp = new C<Base>;
  
    A<BaseVirtual>* avp = new A<BaseVirtual>;
    B<BaseVirtual>* bvp = new B<BaseVirtual>;
    C<BaseVirtual>* cvp = new C<BaseVirtual>;
  
    std::cout << typeToStringMap[a.get_type_index( )] << std::endl;
    std::cout << typeToStringMap[b.get_type_index( )] << std::endl;
    std::cout << typeToStringMap[c.get_type_index( )] << std::endl;
    std::cout << typeToStringMap[av.get_type_index( )] << std::endl;
    std::cout << typeToStringMap[bv.get_type_index( )] << std::endl;
    std::cout << typeToStringMap[cv.get_type_index( )] << std::endl;
  
    std::cout << typeToStringMap[asp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[bsp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[csp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[avsp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[bvsp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[cvsp->get_type_index( )] << std::endl;
  
    std::cout << typeToStringMap[ap->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[bp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[cp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[avp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[bvp->get_type_index( )] << std::endl;
    std::cout << typeToStringMap[cvp->get_type_index( )] << std::endl;
  }
```
This code prints the following
```
Base
Base
Base
ABaseVirtual
BBaseVirtual
CBaseVirtual
Base
Base
Base
ABaseVirtual
BBaseVirtual
CBaseVirtual
Base
Base
Base
ABaseVirtual
BBaseVirtual
CBaseVirtual
```

