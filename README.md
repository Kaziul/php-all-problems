# php-all-problems
//// php api theke data niyeee aseee and database theke data niyeee aseee cuntry code compare kore jodi kono data database ee thake ta hole sei data ar duplicate hoye database eee add hobe na 
 public function currencylayer_fetchall()
    {
        $post['access_key'] = config_item('currencylayer_access_key');
        if (!empty($post['access_key'])) {
            $url = "http://api.currencylayer.com/live";
            $res = curl_request('GET', $url, $post);
            // print('<pre>'.print_r($res,true).'</pre>'); exit;

            $udata = array();
            foreach ($res['quotes'] as $k => $v) {
                $arr = array();
                $arr['code'] = substr($k, 3);
                $arr['xrate'] = $v;
                $arr['base'] = $res['source'];
                $arr['last_syn_date'] = date("Y-m-d H:i:s");
                $arr['syn_from'] = 'currencylayer';
                $udata[] = $arr;
                // $udata1[] = $udata['code'];
            }
            foreach($udata as $value){
                $user_db[]= $value['code'];

            }
            $data_from_db = $this->db->get('tbl_currencies')->result();           
                foreach ($data_from_db as  $values) {
                    $from_db[] = $values->code;
        //   print("<pre>" . print_r($from_db, true) . "</pre>");exit;
                   
                }
            $result = array_diff($user_db, $from_db);
             print("<pre>" . print_r($result, true) . "</pre>");exit;
           
                if(!empty($result)){
                    print("<pre>" . print_r($result, true) . "</pre>");exit;

                    $suc = $this->db->update_batch('tbl_currencies', $udata, 'code');
                    if ($suc) {
                        $return['type'] = 'success';
                        $return['reload'] = '1';
                        echo json_encode($return);
                        exit;
                    }
                }
                
            // }
        } else {
            $return['type'] = "error";
            $return['msg'] = lang('pls_set_your') . ' ' . lang('currencylayer_access_key');
            set_message($return['type'], $return['msg']);
            $return['url'] = base_url() . 'admin/settings/currencylayer';
            echo json_encode($return);
            if (!$this->input->is_ajax_request()) {
                redirect($return['url']);
            }
            exit;
        }
    }
