$('#user_select').select2({
            dropdownParent: $('#exampleModal'),
            placeholder: 'Search for an option...',
            minimumInputLength: 1, // Minimum characters to trigger AJAX search
            ajax: {
                url: url, // Replace with your API endpoint
                dataType: 'json',
                type:'POST',
                delay: 250, // Delay before sending the request in milliseconds
                processResults: function(data) {
                    var array = data.error ? [] : $.map(data, function(m) {
                        return {
                            label: m.value,
                            text: m.value,
                            id: m.id
                        };
                    });
                    return {
                        results: array
                    };
                },
                cache: true,
                headers: {
                    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
                }
            }
        });



$term = $request->input('term');
        // var_dump($term);
        if (!empty($term) && \Auth::user()->can('manage user')) {
            $user = \Auth::user();
            // pr($user->creatorId());
            if ($user->type == 'marketing') {
                $users = User::select('id', 'name')->where('type', '=', 'company')->where('name', 'like', '%'.$term.'%')->where('created_by', '=', $user->creatorId())->get()->toArray();
            }else if ($user->type == 'super admin'){
                $users = User::select('id', 'name')->where('type', '=', 'company')->where('name', 'like', '%'.$term.'%')->get()->toArray();
            }
            // pr($users);
            $user_list = [];
            if(!empty($users)){
                foreach($users as $v){
                    $arr = [];
                    $arr['value'] = $v['id'] . ' - ' . $v['name'];
                    $arr['label'] = $v['name'];
                    $arr['id'] = $v['id'];
                    $user_list[] = $arr;
                    // $user_list[$v['id']] = $v['name'];
                }
            }
            // prx($user_list);
            return response()->json($user_list);
        } else {
            return redirect()->back()->with('error', __('Permission denied.'));
        }
