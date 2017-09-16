#PHP分页 & Thinkphp分页  
***

	<?php
	/* 原生分页 */
	public function pageList(){
		//$logList是索引数组
		$logList = $this->getOptLog($searchArgs);

		//$_GET['page']表示第几页
		if(isset($_GET['page']))
		{
			//$_GET['per']表示每页多少条
			if(isset($_GET['per'])){
				$count = $_GET['per'];
			}else{
				$count = 10;
			}
			$all =  count($logList);
			//array_slice(array,start,length,preserve)
			$logList = array_slice($logList, ($count*($_GET['page'] -1)),$count);
			$start = ($_GET['page']-1) * $count;
			$limit = "$start,$count";
			$pager['all'] = $all;

			if($all == 0){
				$pager['pages'] = 1;
			}else{
				if($all % $count != 0){
					$pager['pages'] = ((int)($all / $count)) +1;
				}else{
					$pager['pages'] = ((int)($all / $count));
				}				
			}
		}
		$this->jret(0,"获取成功",$logList,$pager);
	}

	/* Thinkphp分页1 */
	protected function pageList2($filter){
		$where = "1 = 1";
		//第几页,默认第一页
		$filter['p'] = intval($filter['p']) < 1 ? 1 : intval($filter['p']);

		$Log = M('account_log');
		//每页10条
		$data_list = $Log->where($where)->page($filter['p'],10)->select();
		$count     = $Log->where($where)->count();// 查询满足要求的总记录数
		//传入总记录数和每页显示的记录数
		$Page      = new \Think\Page($count,10);// 实例化分页类 
		$show 	   = $Page->show();//分页显示输出
	}

	/* Thinkphp分页2 */
	public function pageList3(){
		$where['user_id'] = array('eq', get_session('user_id'));

		$userSub = M('userSub');
		$count = $userSub->where($where)->count();
		$Page = new \Think\Page($count, 25);
		$list = $userSub->where($where)->order('create_time desc')->limit($Page->firstRow . ',' . $Page->listRows)->select();

		$this->jret(0, '获取成功', $list, array("all" => $Page->totalRows, "pages" => $Page->totalPages));
	}

