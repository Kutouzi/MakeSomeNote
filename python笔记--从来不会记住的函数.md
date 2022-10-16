## python笔记-->从来不会记住的函数

### 字符串函数

|                             方法                             | 描述                                               |
| :----------------------------------------------------------: | -------------------------------------------------- |
| [capitalize()](https://www.w3school.com.cn/python/ref_string_capitalize.asp) | 把首字符转换为大写。                               |
| [casefold()](https://www.w3school.com.cn/python/ref_string_casefold.asp) | 把字符串转换为小写。                               |
| [center()](https://www.w3school.com.cn/python/ref_string_center.asp) | 返回居中的字符串。                                 |
| [count()](https://www.w3school.com.cn/python/ref_string_count.asp) | 返回指定值在字符串中出现的次数。                   |
| [encode()](https://www.w3school.com.cn/python/ref_string_encode.asp) | 返回字符串的编码版本。                             |
| [endswith()](https://www.w3school.com.cn/python/ref_string_endswith.asp) | 如果字符串以指定值结尾，则返回 true。              |
| [expandtabs()](https://www.w3school.com.cn/python/ref_string_expandtabs.asp) | 设置字符串的 tab 尺寸。                            |
| [find()](https://www.w3school.com.cn/python/ref_string_find.asp) | 在字符串中搜索指定的值并返回它被找到的位置。       |
| [format()](https://www.w3school.com.cn/python/ref_string_format.asp) | 格式化字符串中的指定值。                           |
|                         format_map()                         | 格式化字符串中的指定值。                           |
| [index()](https://www.w3school.com.cn/python/ref_string_index.asp) | 在字符串中搜索指定的值并返回它被找到的位置。       |
| [isalnum()](https://www.w3school.com.cn/python/ref_string_isalnum.asp) | 如果字符串中的所有字符都是字母数字，则返回 True。  |
| [isalpha()](https://www.w3school.com.cn/python/ref_string_isalpha.asp) | 如果字符串中的所有字符都在字母表中，则返回 True。  |
| [isdecimal()](https://www.w3school.com.cn/python/ref_string_isdecimal.asp) | 如果字符串中的所有字符都是小数，则返回 True。      |
| [isdigit()](https://www.w3school.com.cn/python/ref_string_isdigit.asp) | 如果字符串中的所有字符都是数字，则返回 True。      |
| [isidentifier()](https://www.w3school.com.cn/python/ref_string_isidentifier.asp) | 如果字符串是标识符，则返回 True。                  |
| [islower()](https://www.w3school.com.cn/python/ref_string_islower.asp) | 如果字符串中的所有字符都是小写，则返回 True。      |
| [isnumeric()](https://www.w3school.com.cn/python/ref_string_isnumeric.asp) | 如果字符串中的所有字符都是数，则返回 True。        |
| [isprintable()](https://www.w3school.com.cn/python/ref_string_isprintable.asp) | 如果字符串中的所有字符都是可打印的，则返回 True。  |
| [isspace()](https://www.w3school.com.cn/python/ref_string_isspace.asp) | 如果字符串中的所有字符都是空白字符，则返回 True。  |
| [istitle()](https://www.w3school.com.cn/python/ref_string_istitle.asp) | 如果字符串遵循标题规则，则返回 True。              |
| [isupper()](https://www.w3school.com.cn/python/ref_string_isupper.asp) | 如果字符串中的所有字符都是大写，则返回 True。      |
| [join()](https://www.w3school.com.cn/python/ref_string_join.asp) | 把可迭代对象的元素连接到字符串的末尾。             |
| [ljust()](https://www.w3school.com.cn/python/ref_string_ljust.asp) | 返回字符串的左对齐版本。                           |
| [lower()](https://www.w3school.com.cn/python/ref_string_lower.asp) | 把字符串转换为小写。                               |
| [lstrip()](https://www.w3school.com.cn/python/ref_string_lstrip.asp) | 返回字符串的左修剪版本。                           |
|                         maketrans()                          | 返回在转换中使用的转换表。                         |
| [partition()](https://www.w3school.com.cn/python/ref_string_partition.asp) | 返回元组，其中的字符串被分为三部分。               |
| [replace()](https://www.w3school.com.cn/python/ref_string_replace.asp) | 返回字符串，其中指定的值被替换为指定的值。         |
| [rfind()](https://www.w3school.com.cn/python/ref_string_rfind.asp) | 在字符串中搜索指定的值，并返回它被找到的最后位置。 |
| [rindex()](https://www.w3school.com.cn/python/ref_string_rindex.asp) | 在字符串中搜索指定的值，并返回它被找到的最后位置。 |
| [rjust()](https://www.w3school.com.cn/python/ref_string_rjust.asp) | 返回字符串的右对齐版本。                           |
| [rpartition()](https://www.w3school.com.cn/python/ref_string_rpartition.asp) | 返回元组，其中字符串分为三部分。                   |
| [rsplit()](https://www.w3school.com.cn/python/ref_string_rsplit.asp) | 在指定的分隔符处拆分字符串，并返回列表。           |
| [rstrip()](https://www.w3school.com.cn/python/ref_string_rstrip.asp) | 返回字符串的右边修剪版本。                         |
| [split()](https://www.w3school.com.cn/python/ref_string_split.asp) | 在指定的分隔符处拆分字符串，并返回列表。           |
| [splitlines()](https://www.w3school.com.cn/python/ref_string_splitlines.asp) | 在换行符处拆分字符串并返回列表。                   |
| [startswith()](https://www.w3school.com.cn/python/ref_string_startswith.asp) | 如果以指定值开头的字符串，则返回 true。            |
| [strip()](https://www.w3school.com.cn/python/ref_string_strip.asp) | 返回字符串的剪裁版本。                             |
| [swapcase()](https://www.w3school.com.cn/python/ref_string_swapcase.asp) | 切换大小写，小写成为大写，反之亦然。               |
| [title()](https://www.w3school.com.cn/python/ref_string_title.asp) | 把每个单词的首字符转换为大写。                     |
|                         translate()                          | 返回被转换的字符串。                               |
| [upper()](https://www.w3school.com.cn/python/ref_string_upper.asp) | 把字符串转换为大写。                               |
| [zfill()](https://www.w3school.com.cn/python/ref_string_zfill.asp) | 在字符串的开头填充指定数量的 0 值。                |

### 列表函数

|                             方法                             | 描述                                                 |
| :----------------------------------------------------------: | ---------------------------------------------------- |
| [append()](https://www.w3school.com.cn/python/ref_list_append.asp) | 在列表的末尾添加一个元素                             |
| [clear()](https://www.w3school.com.cn/python/ref_list_clear.asp) | 删除列表中的所有元素                                 |
| [copy()](https://www.w3school.com.cn/python/ref_list_copy.asp) | 返回列表的副本                                       |
| [count()](https://www.w3school.com.cn/python/ref_list_count.asp) | 返回具有指定值的元素数量。                           |
| [extend()](https://www.w3school.com.cn/python/ref_list_extend.asp) | 将列表元素（或任何可迭代的元素）添加到当前列表的末尾 |
| [index()](https://www.w3school.com.cn/python/ref_list_index.asp) | 返回具有指定值的第一个元素的索引                     |
| [insert()](https://www.w3school.com.cn/python/ref_list_insert.asp) | 在指定位置添加元素                                   |
| [pop()](https://www.w3school.com.cn/python/ref_list_pop.asp) | 删除指定位置的元素                                   |
| [remove()](https://www.w3school.com.cn/python/ref_list_remove.asp) | 删除具有指定值的项目                                 |
| [reverse()](https://www.w3school.com.cn/python/ref_list_reverse.asp) | 颠倒列表的顺序                                       |
| [sort()](https://www.w3school.com.cn/python/ref_list_sort.asp) | 对列表进行排序                                       |

### 元组函数

|                             方法                             | 描述                                       |
| :----------------------------------------------------------: | ------------------------------------------ |
| [count()](https://www.w3school.com.cn/python/ref_tuple_count.asp) | 返回元组中指定值出现的次数。               |
| [index()](https://www.w3school.com.cn/python/ref_tuple_index.asp) | 在元组中搜索指定的值并返回它被找到的位置。 |

### 集合函数

|                             方法                             | 描述                                         |
| :----------------------------------------------------------: | -------------------------------------------- |
| [add()](https://www.w3school.com.cn/python/ref_set_add.asp)  | 向集合添加元素。                             |
| [clear()](https://www.w3school.com.cn/python/ref_set_clear.asp) | 删除集合中的所有元素。                       |
| [copy()](https://www.w3school.com.cn/python/ref_set_copy.asp) | 返回集合的副本。                             |
| [difference()](https://www.w3school.com.cn/python/ref_set_difference.asp) | 返回包含两个或更多集合之间差异的集合。       |
| [difference_update()](https://www.w3school.com.cn/python/ref_set_difference_update.asp) | 删除此集合中也包含在另一个指定集合中的项目。 |
| [discard()](https://www.w3school.com.cn/python/ref_set_discard.asp) | 删除指定项目。                               |
| [intersection()](https://www.w3school.com.cn/python/ref_set_intersection.asp) | 返回为两个其他集合的交集的集合。             |
| [intersection_update()](https://www.w3school.com.cn/python/ref_set_intersection_update.asp) | 删除此集合中不存在于其他指定集合中的项目。   |
| [isdisjoint()](https://www.w3school.com.cn/python/ref_set_isdisjoint.asp) | 返回两个集合是否有交集。                     |
| [issubset()](https://www.w3school.com.cn/python/ref_set_issubset.asp) | 返回另一个集合是否包含此集合。               |
| [issuperset()](https://www.w3school.com.cn/python/ref_set_issuperset.asp) | 返回此集合是否包含另一个集合。               |
| [pop()](https://www.w3school.com.cn/python/ref_set_pop.asp)  | 从集合中删除一个元素。                       |
| [remove()](https://www.w3school.com.cn/python/ref_set_remove.asp) | 删除指定元素。                               |
| [symmetric_difference()](https://www.w3school.com.cn/python/ref_set_symmetric_difference.asp) | 返回具有两组集合的对称差集的集合。           |
| [symmetric_difference_update()](https://www.w3school.com.cn/python/ref_set_symmetric_difference_update.asp) | 插入此集合和另一个集合的对称差集。           |
| [union()](https://www.w3school.com.cn/python/ref_set_union.asp) | 返回包含集合并集的集合。                     |
| [update()](https://www.w3school.com.cn/python/ref_set_update.asp) | 用此集合和其他集合的并集来更新集合。         |

### 字典函数

|                             方法                             | 描述                                                   |
| :----------------------------------------------------------: | ------------------------------------------------------ |
| [clear()](https://www.w3school.com.cn/python/ref_dictionary_clear.asp) | 删除字典中的所有元素                                   |
| [copy()](https://www.w3school.com.cn/python/ref_dictionary_copy.asp) | 返回字典的副本                                         |
| [fromkeys()](https://www.w3school.com.cn/python/ref_dictionary_fromkeys.asp) | 返回拥有指定键和值的字典                               |
| [get()](https://www.w3school.com.cn/python/ref_dictionary_get.asp) | 返回指定键的值                                         |
| [items()](https://www.w3school.com.cn/python/ref_dictionary_items.asp) | 返回包含每个键值对的元组的列表                         |
| [keys()](https://www.w3school.com.cn/python/ref_dictionary_keys.asp) | 返回包含字典键的列表                                   |
| [pop()](https://www.w3school.com.cn/python/ref_dictionary_pop.asp) | 删除拥有指定键的元素                                   |
| [popitem()](https://www.w3school.com.cn/python/ref_dictionary_popitem.asp) | 删除最后插入的键值对                                   |
| [setdefault()](https://www.w3school.com.cn/python/ref_dictionary_setdefault.asp) | 返回指定键的值。如果该键不存在，则插入具有指定值的键。 |
| [update()](https://www.w3school.com.cn/python/ref_dictionary_update.asp) | 使用指定的键值对字典进行更新                           |
| [values()](https://www.w3school.com.cn/python/ref_dictionary_values.asp) | 返回字典中所有值的列表                                 |

### 数组函数

|                             方法                             | 描述                                                 |
| :----------------------------------------------------------: | ---------------------------------------------------- |
| [append()](https://www.w3school.com.cn/python/ref_list_append.asp) | 在列表的末尾添加一个元素                             |
| [clear()](https://www.w3school.com.cn/python/ref_list_clear.asp) | 删除列表中的所有元素                                 |
| [copy()](https://www.w3school.com.cn/python/ref_list_copy.asp) | 返回列表的副本                                       |
| [count()](https://www.w3school.com.cn/python/ref_list_count.asp) | 返回具有指定值的元素数量。                           |
| [extend()](https://www.w3school.com.cn/python/ref_list_extend.asp) | 将列表元素（或任何可迭代的元素）添加到当前列表的末尾 |
| [index()](https://www.w3school.com.cn/python/ref_list_index.asp) | 返回具有指定值的第一个元素的索引                     |
| [insert()](https://www.w3school.com.cn/python/ref_list_insert.asp) | 在指定位置添加元素                                   |
| [pop()](https://www.w3school.com.cn/python/ref_list_pop.asp) | 删除指定位置的元素                                   |
| [remove()](https://www.w3school.com.cn/python/ref_list_remove.asp) | 删除具有指定值的项目                                 |
| [reverse()](https://www.w3school.com.cn/python/ref_list_reverse.asp) | 颠倒列表的顺序                                       |
| [sort()](https://www.w3school.com.cn/python/ref_list_sort.asp) | 对列表进行排序                                       |

