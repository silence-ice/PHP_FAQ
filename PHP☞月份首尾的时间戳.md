#PHP☞月份首尾时间戳  
***
	
	/**
	 * 判断月份共有多少天
	 * @param $in, $agentId
	 * @return array
	 */
	protected function yearMonthDays($year,$month){
		if(empty($year) || empty($month)){
			$this->jret(1,'时间参数传递失败');
			exit;
		}
	
	    if (in_array($month, array(1, 3, 5, 7, 8, 01, 03, 05, 07, 08, 10, 12))) {  
	        return '31';  
	    }elseif ($month == 2){  
	        if ($year % 400 == 0 || ($year % 4 == 0 && $year % 100 !== 0)) {        //判断是否是闰年  
	            return '29';  
	        }else{  
	            return '28';  
	        }  
	    } else {  
	        return '30';  
	    }
	}
	
	/**
	 * 计算月份开始和结束的时间戳
	 * @param $in, $agentId
	 * @return array
	 */
	protected function getMonthStamp($start_year, $end_year, $start_month, $end_month){
		if(!isset($start_year) || !isset($end_year) || !isset($start_month) || !isset($end_month)){
			$this->jret(1,'时间参数传递失败');
			exit;
		}
	
		//针对年初的第一个月开始计算
		if ($start_month == 0) {
			$start_year--;
			$start_month = 12;
		}
	
		if ($end_month == 0) {
			$end_year--;
			$end_month = 12;
		}
	
		$first_start_time = mktime(0, 0, 0, $start_month, 0, $start_year);//开始月份的前一天
		$first_start_time = $first_start_time+86400;//开始月份第一天的开始
	
		$last_start_time = mktime(0, 0, 0, $end_month, 0, $end_year);//结束月份的前一天
		$last_start_time = $last_start_time+86400;//结束月份第一天的开始
		$year_month_days = $this->yearMonthDays($end_year, $end_month);//获取该月共有多少天
		$end_time = $last_start_time+$year_month_days*86400-1;//月份的结束
	
		$data["start_time"] = $first_start_time;
		$data["end_time"] = $end_time;
		return $data;
	}