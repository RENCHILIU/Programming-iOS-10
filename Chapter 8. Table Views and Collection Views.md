# Chapter 8. Table Views and Collection Views


## Table Views

### 关于cell的重用机制
    Cell作为reuse cell， 即使你有1000 cell， 也不会一次性创建所有的cell
    
    获取tableviewCell的方式就是开始4中style中的一种：
    .default -> UILabel + UIImageView(opt)
    .value1 -> 2UILabels(textLabel<right-aligned>+detailTextLabel<left-aligned>) + UIImageView(opt)
    .value2 -> 2UILabels(textLabel<right-aligned>+detailTextLabel<left-aligned>)
    .subtitle -> 2UILabels(textLabel<above>+detailTextLabel<left-aligned>) + UIImageView(opt)
    
### The world’s simplest table

    
### Cell中部分可定制style
    textColor, highlightedTextColor
    textAlignment
    numberOfLines
    font: cell.textLabel!.font = UIFont(name:"Helvetica-Bold", size:12.0)
    shadowColor, shadowOffset
    cell image
    accessoryView
    indentationLevel, indentationWidth
    separatorInset
    selectionStyle
    backgroundColor
    backgroundView
    selectedBackgroundView
    multipleSelectionBackgroundView
    
### TableView 可用的配置
    rowHeight
    separatorStyle, separatorColor, separatorInset
    backgroundColor, backgroundView
    tableHeaderView, tableFooterView

### 初始化Cell的两个方法
    NO1: dequeueReusableCell(withIdentifier:)
    
    NO2: dequeueReusableCell(withIdentifier:for:)
    
###### NO2优点：
    The result is never nil ：没有cell会自动create
    The cell size is known earlier ：  从rowHeight 或者 delegate’s tableView(_:heightForRowAt:) 得知
    The identifier is consistent ：相比第一个方法，两个方法都要先申明register(_:forCellReuseIdentifier:)（除非cell来自storyboard）。 但是当你传递了错误的reuse identifier， 第二个方法会报错。 第一个方法则不会用错误的identifier（依然跑程序）。
    
### 注意事项
    在viewdidload中registerCell（除非cell来自storyboard）
    如果当前ViewController不是UITableViewController，那么指明添加tableview的delegate和datasource
    
###  4中方法去自定义Cell
######      Overriding a cell’s subview layout：
     
    在自定义的UITableViewCell中重写layoutSubviews
        
######      Adding subviews in code：
     
    在tableView(_:cellForRowAt:)方法中添加subView
    cell.contentView.addSubview(iv)
        
######      Designing a cell in a nib

    新建一个xib， 选择view。 拖出一个tableviewCell
    （尽管xib中cell的height可以调整，但是还是取决于rowHeight 或者 tableView(_:heightForRowAt:)）
    
    可以给每一个view一个tag，这样在tableView(_:heightForRowAt:)中就可以使用viewWithTag(_:)
    
    register的时候，提供nibname
    self.tableView.register(UINib(nibName:"MyCell", bundle:nil), forCellReuseIdentifier: "Cell")
    
    如果不仅仅是提供xib，还提供了class，那么可以用property那么来获取
    let cell = tableView.dequeueReusableCell(withIdentifier:"Cell", for: indexPath) as! MyCell // *
        
######      Designing a cell in a storyboard
    不需要callregister(_:forCellReuseIdentifier:)! 
    在使用dequeueReusableCell(withIdentifier:for:)时候，只需要提供storyboard中 cell 所相应match的indentifier
    let cell = tableView.dequeueReusableCell(withIdentifier:"Cell", for: indexPath) as! MyCell
    
###     Table View Data
###### 关于tableview的data setup 原则
    Be ready
    Be fast
    Be consistent


###     Table View 的一些补充
###### TableView初始化三大件
    numberOfSections(in:) (默认是0)
    tableView(_:numberOfRowsInSection:)
    tableView(_:cellForRowAt:)
    
###### Cell重用注意事项
    if item.enclosures != nil && item.enclosures.count > 0 {
    cell.speaker.isHidden = false
    }
    // 当cell重用的时候，speaker.isHidden会继承之前的那个cell的性质

    //解决， 对于每一个cell，每一次都自己考虑一遍
    cell.speaker.isHidden =
        !(item.enclosures != nil && item.enclosures.count > 0)
        
###     Table View Sections
###### section 方法
         numberOfSections(in:)
         
###### UITableViewHeaderFooterView 属性
         textLabel
         detailTextLabel
         contentView 
         backgroundView
         
         Don’t set a UITableViewHeaderFooterView’s backgroundColor; instead, set the backgroundColor of its contentView（UITableViewHeaderFooterView 有一个contentView的属性）
         
###### UITableViewHeaderFooterView 重用机制
        代理方法：
        tableView(_:titleForHeaderInSection:)
        tableView(_:titleForFooterInSection:)         
        tableView(_:viewForHeaderInSection:)
        tableView(_:viewForFooterInSection:)
        
        方法调用：
        register(_:forHeaderFooterViewReuseIdentifier:) 
        dequeueReusableHeaderFooterView(withIdentifier:) 使用这个
        
        方法配置：
        tableView(_:willDisplayHeaderView:forSection:)
        tableView(_:willDisplayFooterView:forSection:)
###### Section Data  load
        同cell重用机制差不多，都是进行data load，reuse   
        label例子：
        View例子：
        
          
###### Section Index   
        sectionIndexTitles(for tv: UITableView)
        属性：
        sectionIndexColor
        sectionIndexBackgroundColor
        sectionIndexTrackingBackgroundColor
        
### Refreshing a Table View         
        reload table view 不是regenerate 所有的data。
        因为：
        in a table that caches reusable cells, there are no cells of interest other than those actually showing in the table at this moment.
        
###### reload的方法
        reloadData
        reloadRows(at:with:)
        reloadSections(_:with:)
        
        后两者对于reload还要动画效果（with：后面）
        .fade
        .right, .left, .top, .bottom
        .none
        .middle
        .automatic
        
        
###### Direct Access to Cells
        tableView.cellForRow(at:)
        可以直接获取cell， 但是如果你修改了cell，你也要对于的修改model。 不然下次reload，reuse会出错。
        
        some UITableView properties and methods:
        visibleCells
        indexPathsForVisibleRows
        cellForRow(at:)
        indexPath(for:)


###### Refresh Control
        使用Refresh Control 进行数据刷新
        
        


### 动态设置Cell的高度方法 Variable Row Heights   
**oldest to newest but also from fastest to slowest**   
     the delegate’s tableView(_:heightForRowAt:) 

###### Manual Row Height Measurement
       每一个cell都先调用前一个，紧跟着后一个方法：
       tableView(_:heightForRowAt:) 会先于  tableView(_:heightForRowAt:)  最后 return cell
       在tableView(_:heightForRowAt:) 中计算多，会影响速度，可以先给个fake数组值，然后回到tableView(_:heightForRowAt:)在return cell前调用 setupCell的方法计算。
       
###### Measurement and Layout with Constraints
       because every subview either is given explicit size constraints or else is the kind of view that has an implicit size based on its contents, like a label or an image view — then we can simply call systemLayoutSizeFitting(_:) to tell us the resulting height of the cell.
###### Estimated Height

    iOS7后三种属性&方法：
        estimatedRowHeight
        estimatedSectionHeaderHeight
        estimatedSectionFooterHeight  
        tableView(_:estimatedHeightForRowAt:)
        tableView(_:estimatedHeightForHeaderInSection:)
        tableView(_:estimatedHeightForFooterInSection:)    

    The estimated height is set in viewDidLoad:
         self.tableView.estimatedRowHeight = 75
         
###### Automatic Row Height
    in iOS 8 中引入
    
    使用：
    using autolayout to determine the size of the contentView from the inside out
    set the table view’s estimatedRowHeight and don’t implement tableView(_:heightForRowAt:) at all！！！
    
    self.tableView.rowHeight = UITableViewAutomaticDimension
    self.tableView.estimatedRowHeight = 75
    
    优点：
    对于动态高度的UILabel很好用。（UILabels whose height will depend upon their text contents）
    
    对于customize的View， override intrinsicContentSize
    class MyView : UIView {
    var h : CGFloat = 200 {
        didSet {
            self.invalidateIntrinsicContentSize()
        }
    }
    override var intrinsicContentSize: CGSize {
        return CGSize(width:300, height:self.h)
       }
    }


### Table View Cell Selection
###### properties：
        indexPathForSelectedRow
        indexPathsForSelectedRows
        These read-only properties report the currently selected row(s), or nil if there is no selection
###### methods
selectRow(at:animated:scrollPosition:)
deselectRow(at:animated:)

###### 注意
To deselect all currently selected rows, call selectRow(at:...) with a nil index path.
Selection 不会因为reuse消失，但是reload 会deselect所有

###### Responding to Cell Selection
方法：
    tableView(_:shouldHighlightRowAt:)
    
    tableView(_:didHighlightRowAt:)
    
    tableView(_:didUnhighlightRowAt:)
    
    tableView(_:willSelectRowAt:)
    
    tableView(_:didSelectRowAt:)
    
    tableView(_:willDeselectRowAt:)
    
    tableView(_:didDeselectRowAt:)
    
    区分 Highlight 和 select。 


###### Navigation from a Table View
    tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)
  
### 补充  
###### Table View Scrolling and Layout
    A UITableView is a UIScrollView
    
    More convenience method：
        scrollToRow(at:at:animated:)
        scrollToNearestSelectedRow(at:animated:)
    
###### Table View State Restoration
    modelIdentifierForElement(at:in:)
    indexPathForElement(withModelIdentifier:in:)



### Table View Searching
    使用UISearchController ：
        [关于UISearchController的简单使用](https://www.jianshu.com/p/4dd662f4230a)
    

### Table View Editing
    方法：
    setEditing(_:animated:)

    tableView(_:canEditRowAt:)
    tableView(_:editingStyleForRowAt:)
    .delete
    .insert
    .none
    
    在下面的tableview方法
        insertRows(at:with:)
        deleteRows(at:with:)
        insertSections(_:with:)
        deleteSections(_:with:)
        moveSection(_:toSection:)
        moveRow(at:to:)
        
    **Data，model。必须同步更新！**    
    
    
    
##     Collection Views

###     Collection Views配置
###### 三个拷问和答案
        numberOfSections(in:)
        collectionView(_:numberOfItemsInSection:)
        collectionView(_:cellForItemAt:)

        dequeueReusableCell(withReuseIdentifier:for:)

###### 与tableView的区别
        The big difference between a table view and a collection view is how the
        collection view lays out its elements (cells and supplementary views). 
         A collection view has no such rules. In fact, a collection view doesn’t
        lay out its elements at all! That job is left to another class, 
        a subclass of UICollectionViewLayout.
        
        
    
        
    
###### UICollectionViewLayout方法
        collectionViewContentSize
        layoutAttributesForElements(in:)
        
    UICollectionViewDelegateFlowLayout Method：
            collectionView(_ collectionView: UICollectionView,layout lay:   UICollectionViewLayout,sizeForItemAt indexPath: IndexPath)
            collectionView(_ collectionView: UICollectionView,layout lay: UICollectionViewLayout,referenceSizeForHeaderInSection section: Int)

###### Delete cell， rearrange cell

